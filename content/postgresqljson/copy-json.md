---
title: 'How to load JSON data in PostgreSQL with the COPY command'
date: "2024-02-02T11:25:54+01:00"
url: "/postgresqljson/load-json-data-with-postgresql-copy"
description: "How to load JSON data in PostgreSQL with the COPY command"
tldr: "How to load JSON data in PostgreSQL with the COPY command"
image: "/images/2024/pgcopyjson.png"
credit: "ftisiot"
featured_image: "/images/2024/pgcopyjson.png"
show_reading_time: true
categories:
- PostgreSQL
- JSON
- COPY
- jq
---

You have a JSON dataset that you want to upload to a PostgreSQL table containing properly formatted rows and columns... How do you do it? 

All the main sources like [my own blog](/postgresqljson/how-to-load-json-postgresql) and [others](https://konbert.com/blog/import-json-into-postgres-using-copy) tell you to load the JSON in a dedicated temporary table containing a unique `JSON` column, then parse it and load into the target table. However there could be another way, avoiding the temp table!

<!--more-->

![Traditional JSON Load involving a temp table](/images/2024/traditional-json-load.png)

In this blog we'll see how to upload the JSON directly using PostgreSQL `COPY` command and using an utility called [jq](https://jqlang.github.io/jq/)!

{{< code "/components/pglink" >}}

## PostgreSQL COPY command

First of all, let's check PostgreSQL [COPY command](https://www.postgresql.org/docs/current/sql-copy.html). It's a command that allows you to copy into a PostgreSQL table data from a file, there are two versions:

* `COPY` if the file is sitting in the PG server already
* `\COPY` if the file is sitting in the client machine connected to the server via `psql`

In both cases, the standard `COPY` command has the following minimal set of parameters:

```sql
\copy <TARGET TABLE>(<OPTIONAL LIST OF COLUMNS>) FROM <SOURCE FILE> <FORMAT>
```

Where:

* `<TARGET TABLE>` is the name of the target table
* `(<OPTIONAL LIST OF COLUMNS>)` defines the columns in the table to load
* `<SOURCE FILE>` is pointing to the source file to load
* `<FORMAT>` defines the format of the data

> The full list of parameters is available in the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)

### PostgreSQL COPY command - the out of the box formats

Let's focus on the formats available in the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html) are listed:

* `TEXT`: can be used to load a full text
* `CSV`: to load comma (or otherwise separated) values
* `BINARY`: to load binary files

Unfortunately there doesn't seem to be an out of the box way to load JSON files!

## The `PROGRAM` option to the rescue!

A, maybe not known, option of the `COPY` command is to point to a program to execute instead of the file. This option can be called with the following command:

```
\copy <TARGET TABLE>(<OPTIONAL LIST OF COLUMNS>) FROM PROGRAM "<SET OF INSTRUCTIONS>" 
```

Compared to the previous `\copy` call, this time we are adding the `PROGRAM` with a set of instructions delimited by quotes or double quotes that will be executed on the client machine before loading the data.

> If you are using the `COPY` command on the server, you'll probably need a superuser. This is the error message shown in [Aiven](https://go.aiven.io/francesco-signup): `ERROR:  must be superuser or have privileges of the pg_read_server_files role to COPY from a file`

So, what we can do, is to reshape the data before loading it.

### jq - the indispensable JSON parsing tool

I've been using jq quite a while in a lot of blog posts, it's a very handy tool to parse, reshape, select JSON documents. For the purpose of this blog, we'll use to reshape the JSON input into a CSV format, digestible from the PostgreSQL `COPY` command. 

> You need to have [jq](https://jqlang.github.io/jq/) installed on the workstation from where the `COPY` command is executed!

Let's create a basic JSON file with named `test.json` with the following content:

```json
{
    "id":1,
    "mystring":"ciao"
}
{
    "id":2,
    "mystring":"sole"
}
{
    "id":3,
    "mystring":"mare"
}
```

With jq we can read and reshape the above JSON to a CSV format with:

```bash
more test.json | jq -r ". | [.id, .mystring] | @csv"
```

In the above command:

* `more test.json` reads the file
* `jq -r` prints the raw output
* the first `.` selects all the elements at the root level
* `| [.id, .mystring]` retrieves the `id` and `mystring` keys from each element
* `|@csv` sets the output format as CSV

The output is:

```CSV
1,"ciao"
2,"sole"
3,"mare"
```

> To check the complete set of options available with jq please view the [manual](https://jqlang.github.io/jq/manual/)


## Stitching all together

So, how can we load a target table with just 1 `COPY` command? Let's first create the target table with:

```sql
CREATE TABLE MYTARGETTABLE (id serial, myid int, mystring text);
```

We now want to load the `myid` and `mystring` columns of the `MYTARGETTABLE` table with the following `COPY` command reading from the `test.json` and applying the transformation with `jq`.


```sql
\copy MYTARGETTABLE(myid, mystring) 
FROM PROGRAM 'more test.json | jq -r ". | [.id, .mystring] | @csv"'  
CSV
```

The output is the data properly loaded in the `MYTARGETTABLE` table

```
 id | myid | mystring
----+------+----------
  1 |    1 | ciao
  2 |    2 | sole
  3 |    3 | mare
(3 rows)
```

Solving problems at the source sometimes is useful to avoid extra hops! Stitching together `COPY` (with `PROGRAM`) and `jq` provides us the flexibility to load JSON files without intermediary tables.