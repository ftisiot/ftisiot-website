---
title: '1 billion rows challenge in PostgreSQL and ClickHouse'
date: "2024-01-03T16:43:29+01:00"
url: "/posts/1brows"
description: "Sorting 1 billion rows challenge with PostgreSQL and ClickHouse"
tldr: "Sorting 1 billion rows challenge with PostgreSQL and ClickHouse"
image: "/images/2024/query.png"
credit: "ftisiot"
thumbnail: "/images/2024/query.png"
categories:
- PostgreSQL
- ClickHouse
- Sorting
---

Last week the good old [Gunnar Morling](https://www.linkedin.com/in/gunnar-morling-2b44b7229/) launched an interesting [challenge](https://www.morling.dev/blog/one-billion-row-challenge/) about ordering 1 billion rows in Java. Like my ex colleague and friend [Robin Moffat](https://www.linkedin.com/in/robinmoffatt/), I'm not at all a Java expert, and while [Robin used DuckDB](https://rmoff.net/2024/01/03/1%EF%B8%8F%E2%83%A3%EF%B8%8F-1brc-in-sql-with-duckdb/) to solve the challenge, I did the same with PostgreSQL and ClickHouse.

<!--more-->

> Alert: the following is **NOT** a benchmark! The test is done with default installations of both databases and NO optimization. The blog only shows the technical viability of a solution.

## Generate the data

I used pretty much the same steps as [Robin to generate the data](https://rmoff.net/2024/01/03/1%EF%B8%8F%E2%83%A3%EF%B8%8F-1brc-in-sql-with-duckdb/#setup)

* Forked the repository and cloned locally
* Installed Java21 to generate the data

```bash
sdk install java 21.0.1-zulu
sdk use java 21.0.1-zulu
```

* Built the data generator

```bash
./mvnw clean verify
```

* Generated some rows

```
./create_measurements.sh 1000000000
```

The above generates a file named `measurements.txt` with the 1 billion rows.

## PostgreSQL

### Setup local PostgreSQL

How do I load a local file into a PostgreSQL database? Well, it depends where the PostgreSQL database is. For my test I could have used [Aiven](https://go.aiven.io/francesco-signup) but decided for a local installation on my Mac.

I installed PostgreSQL 16 with

```bash
brew install postgres@16
```

I can check how to start PostgreSQL locally with:

```bash
brew info postgresql@16
```

It tells me to execute:

```bash
LC_ALL="C" /usr/local/opt/postgresql@16/bin/postgres -D /usr/local/var/postgresql@16
```

Once run we should see the welcoming message `LOG:  database system is ready to accept connection`

## Load the data in PostgreSQL

With PostgreSQL running with all the default values (read more [here](https://www.postgresql.org/docs/16/app-initdb.html)) we are ready to upload the data.

We can connect to our PostgreSQL database with

```bash
psql postgres
```

Next step is to create a table containing our data with

```SQL
CREATE UNLOGGED TABLE TEST(CITY TEXT, TEMPERATURE FLOAT);
```

We are using the `UNLOGGED` [parameter](https://www.crunchydata.com/blog/postgresl-unlogged-tables) to speed up the copy of the data, however we are assuming the risk of not writing any WAL log in the process... probably not the smartest idea if this is sensible data we'll want to reuse later. We can load the data with the `COPY` command:

```sql
\copy TEST(CITY, TEMPERATURE) FROM 'measurements.txt' DELIMITER ';' CSV;
```

### PostgreSQL Results

Following the [same reasoning as Robin](https://rmoff.net/2024/01/03/1%EF%B8%8F%E2%83%A3%EF%B8%8F-1brc-in-sql-with-duckdb/#formatting-the-output), I was able to get to a query like

```SQL
WITH AGG AS(
    SELECT city, 
           MIN(temperature) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           MAX(temperature) max_measure
    FROM test 
    GROUP BY city
    LIMIT 5)
SELECT STRING_AGG(CITY || '=' || CONCAT(min_measure,'/', mean_measure,'/', max_measure),', ' ORDER BY CITY) 
FROM AGG
;
```

The main difference, compared to DuckDB is on the usage of the `STRING_AGG` function that allows me to directly create the string ordered by `CITY` with all the metrics.

### PostgreSQL Timing

To get the timing I created a file called `test.sql` with the entire set of commands:

```sql
\timing
\o /tmp/output
-- Load the data
DROP TABLE TEST;
CREATE UNLOGGED TABLE TEST(CITY TEXT, TEMPERATURE FLOAT);
COPY TEST(CITY, TEMPERATURE) FROM '<PATH_TO_FILE>/measurements.txt' DELIMITER ';' CSV;

-- Run calculations
WITH AGG AS(
    SELECT city,
           MIN(temperature) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           MAX(temperature) max_measure
    FROM test
    GROUP BY city
    )
SELECT STRING_AGG(CITY || '=' || CONCAT(min_measure,'/', mean_measure,'/', max_measure),', ' ORDER BY CITY)
FROM AGG
;
```

And then timed it with:

```bash
time psql postgres -f test.sql
```

The timing was a, not astonishing, `9m16.135s` with the majority (`6m:24.376s` spent on copying the data) and `2m:51.443s` on aggregating.

### Edit Using PostgreSQL Foreign Data Wrapper (FDW)

As [suggested on HN](https://news.ycombinator.com/item?id=38865177) by using a File Foreign data wrapper we could eliminate the need of loading the data in the PostgreSQL table.

The `test.sql` has been changed to:

```sql
\timing
\o /tmp/output
-- Load the data
DROP TABLE TEST;

CREATE EXTENSION file_fdw;
CREATE SERVER stations FOREIGN DATA WRAPPER file_fdw;
CREATE FOREIGN TABLE TEST (
  city text,
  temperature float
) SERVER stations OPTIONS (filename '<PATH>/measurements.txt', format 'csv', delimiter ';');

-- Run calculations
WITH AGG AS(
    SELECT city,
           MIN(temperature) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           MAX(temperature) max_measure
    FROM test
    GROUP BY city
    )
SELECT STRING_AGG(CITY || '=' || CONCAT(min_measure,'/', mean_measure,'/', max_measure),', ' ORDER BY CITY)
FROM AGG
;
```

Where the biggest change is that now the `TEST` table is defined as `FOREIGN TABLE` pointing directly to the `measurements.txt` file. 
With The File FDW we removed the need of uploading the data to a table (that was costing us more than 6 minutes), but now the overall query takes `8m:24.572s`. Overall, compared to the copy and query solution, we are `1` min faster.

## ClickHouse

## Setup local ClickHouse 

How do I load a local file into a ClickHouse database? Well, it depends where the ClickHouse database is. For my test I could have used [Aiven](https://go.aiven.io/francesco-signup) but decided for a local installation on my Mac.

I installed ClickHouse locally with ([source](https://clickhouse.com/docs/en/operations/utilities/clickhouse-local))

```bash
 curl https://clickhouse.com/ | sh
```

## Query the data in ClickHouse

Compared to PostgreSQL, ClickHouse allows me to directly querying the CSV file without loading the data in a table.

I can query the `measurement.txt` file with:

```bash
./clickhouse local -q "SELECT c1 as city, c2 as measurement FROM file('measurements.txt', CSV) LIMIT 5"  --format_csv_delimiter=";"
```

In the above I defined:
* a pointer to a file called `measurements.txt` in `CSV` format
* a custom delimiter `;`
* the first column `c1` as `city`
* the second column `c2` as measurement

The input is correctly parsed.

```
Wellington	12
Riga	0.30000000000000004
Palermo	18.4
Sochi	10.8
Accra	11.7
```

### ClickHouse Results

Following the [same reasoning as Robin](https://rmoff.net/2024/01/03/1%EF%B8%8F%E2%83%A3%EF%B8%8F-1brc-in-sql-with-duckdb/#formatting-the-output), I was able to get to a query like:

```sql
WITH AGG AS(
    SELECT city,
           cast(MIN(temperature) AS DECIMAL(8,1)) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           cast(MAX(temperature) AS DECIMAL(8,1)) max_measure
    FROM (SELECT c1 as city, c2 as temperature FROM file('measurements.txt', CSV))
    GROUP BY city
    ORDER BY city
    )
SELECT arrayStringConcat(groupArray(city || '=' || CONCAT(min_measure,'/', mean_measure,'/', max_measure)), ', ')
FROM AGG
```

The main difference, compared to the PostgreSQL solution is to use `groupArray` to create the list of cities (see the `ORDER BY city` in the first query to order them correctly), and the `arrayStringConcat` to concatenate the array elements in a string.


### ClickHouse Timing

To get the timing I executed the following:

```bash
time ./clickhouse local -q """WITH AGG AS(
    SELECT city,
           cast(MIN(temperature) AS DECIMAL(8,1)) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           cast(MAX(temperature) AS DECIMAL(8,1)) max_measure
    FROM (SELECT c1 as city, c2 as temperature FROM file('measurements.txt', CSV))
    GROUP BY city
    ORDER BY city
    )
SELECT arrayStringConcat(groupArray(city || '=' || CONCAT(min_measure,'/', mean_measure,'/', max_measure)), ', ')
FROM AGG"""  --format_csv_delimiter=";"
```

The result is `44.465s`!

## Conclusion

Both PostgreSQL and ClickHouse were able to complete the challenge. PostgreSQL initial solution had the limitiation of forcing the upload of data into a table that took most of the time, when using the file FDW the performances still weren't great. ClickHouse, on the other side, was able to query directly the CSV and got much faster results, as expected from a database designed for analytics.

> Alert: the above is **NOT** a benchmark! The test is done with default installations of both databases and NO optimization. The blog only shows the technical viability of the solution.
