---
title: '1 billion rows challenge in MySQL'
date: "2024-01-26T14:43:29+01:00"
url: "/posts/1browsmysql"
description: "Sorting 1 billion rows challenge with MySQL"
tldr: "Sorting 1 billion rows challenge with MySQL"
image: "/images/2024/mysql1brc.png"
credit: "ftisiot"
featured_image: "/images/2024/query.png"
show_reading_time: true
categories:
- MySQL
- Sorting
- 1brows
---

Earlier this month I wrote a piece on solving [Gunnar Morling](https://www.linkedin.com/in/gunnar-morling-2b44b7229/) interesting [1 billion rows challenge](https://www.morling.dev/blog/one-billion-row-challenge/) in [PostgreSQL and ClickHouse](/posts/1brows). Since [Aiven](http://aiven.io) provides also MySQL, I wanted to give it a try. TLDR; The results are much slower than PG and ClickHouse, do you have any suggestion on how to improve?

<!--more-->

> Alert: the following is **NOT** a benchmark! The test is done with default installations of both databases and NO optimization. The blog only shows the technical viability of a solution.

{{< code "/components/mysqllink" >}}

## Install MySQL on Mac

The first step is to have an handy MySQL, I could use the [Aiven FREE tier](https://go.aiven.io/francesco-signup), but, as for the previous PostgreSQL and ClickHouse example, decided to install it locally. To do it, you can execute:

```bash
brew install mysql
```

then we can start with 

```bash
brew services start mysql
```

## Connect to MySQL and setup a database

Once the installation process is completed, we can connect to the local MySQL with the following command:

```bash
mysql -u root
```

> For this test we're going to use the `root` user. However is highly suggested to create a non root user for any sensible work with a database.

We can now create a database with:

```sql
CREATE DATABASE 1brow;
```

And start using it with:

```sql
USE 1brow;
```

### Loading the data in MySQL

In order to properly solve the challenge we need to load the data into MySQL. We can create a table called `TEST` with the needed columns with:

```sql
CREATE TABLE TEST (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  city VARCHAR(100),
  temperature FLOAT
);
```

And populate it with:

```sql
LOAD DATA LOCAL INFILE "measurements.txt.new" IGNORE
INTO TABLE TEST
COLUMNS TERMINATED BY ';'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```

If you receive the error `ERROR 3948 (42000): Loading local data is disabled; this must be enabled on both the client and server sides`, we can enable it with:

```sql
SET GLOBAL local_infile=1;
```

Compared to [PostgreSQL and ClickHouse](/posts/1brows), MySQL doesn't seem to have a native option to read from an external `CSV` file. The [MySQL CSV Engine](https://dev.mysql.com/doc/refman/8.0/en/csv-storage-engine.html) is available, but it requires you to move the CSV within MySQL's data folder.

### MySQL Results

Once the data is loaded, we can use a similar query to the [PostgreSQL and ClickHouse](/posts/1brows) ones to perform the correct ordering.


```sql
WITH AGG AS(
    SELECT city,
           MIN(temperature) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           MAX(temperature) max_measure
    FROM test
    GROUP BY city
    )
SELECT GROUP_CONCAT(CITY, '=', CONCAT(min_measure,'/', mean_measure,'/', max_measure) ORDER BY CITY SEPARATOR ', ')
FROM AGG
;
```

The above query is using `GROUP_CONCAT` allows to return the concatenated list of cities once min/mean/max measures have been calculated.

## MySQL Timing

To get the timing I created a file called `test.sql` with the entire set of commands:

```sql
CREATE TABLE TEST (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  city VARCHAR(100),
  temperature float
) ENGINE=MEMORY;

SET GLOBAL local_infile=1;

LOAD DATA LOCAL INFILE "measurements.txt" IGNORE 
INTO TABLE TEST
COLUMNS TERMINATED BY ';'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(city, temperature);

WITH AGG AS(
    SELECT city,
           MIN(temperature) min_measure,
           cast(AVG(temperature) AS DECIMAL(8,1)) mean_measure,
           MAX(temperature) max_measure
    FROM test
    GROUP BY city
    )
SELECT GROUP_CONCAT(CITY, '=', CONCAT(min_measure,'/', mean_measure,'/', max_measure) ORDER BY CITY SEPARATOR ', ')
FROM AGG
;
```

And a then timed with:

```bash
time mysql --local-infile=1 -u root -vvv 1brow < test.sql
```

The timing is `43 min 57.99 sec` with over `33 min 32.15 sec` spent on the ingestion phase and `10 min 25.77 sec` on the query. The file `test.ibd` being over `40GB` almost 4x times the original size of the `measurements.txt` file (`13GB`). You can check the size of the table file with the following command:

```bash
ls -lh /usr/local/var/mysql/1brow/test.idb
```

> Alert: the following is **NOT** a benchmark! The test is done with default installations of both databases and NO optimization. The blog only shows the technical viability of a solution.

You might need to tweak the `connect_timeout` parameter in `my.cnf` to raise the connection timeout to 100 minutes in order to wait for the loading and query process to finish.

## Speed up MySQL timing by using the MEMORY Engine

MySQL has a [Memory storage engine](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html) which will avoid writing to disk and store the entire table in RAM. As per documentation, this is not a safe option for production workloads since:

> Because the data is vulnerable to crashes, hardware issues, or power outages, only use these tables as temporary work areas or read-only caches for data pulled from other tables.

The only change we have to perform, in order to use the `MEMORY` storage engine is to redefine our `test` table as:

```sql
CREATE TABLE TEST (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  city VARCHAR(100),
  temperature FLOAT
) ENGINE = MEMORY;
```

But, when making the change and retrying the script you might hit the `ERROR 1114 (HY000) at line 9: The table 'test' is full` since the data doesn't fit the memory allocated to MySQL. To avoid this we can set the following flags to raise the allocated memory to `64GB`:

```sql
SET max_heap_table_size = 1024 * 1024 * 1024 * 64;
```

However, also this didn't work, and stopped my test after waiting for over `40 minutes` for just the loading time. 

Do you have ideas on how to speed up the process in MySQL? I'm all 👂 on X (ex Twitter) @ftisiot!


