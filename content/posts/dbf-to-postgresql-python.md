---
title: 'From dbf to SQL (and PostgreSQL) with Python'
date: "2024-03-04T14:31:10+01:00"
url: "/posts/dbf-to-postgresql-python"
description: "Translate from dbf to SQL with Python"
tldr: "Translate from dbf to SQL with Python"
image: "/images/2024/dbf-pg.png"
credit: "ftisiot"
featured_image: "/images/2024/dbf-pg.png"
show_reading_time: true
categories:
- dbf
- SQL
- Python
- PostgreSQL
---

Some time ago I found an interesting database file suffix I never faced before: the `.dbf` and saw around that it was first introduced in 1983 with dBASE II. This article showcases how we can automatically generate the PostgreSQL table and fill it with data using Python and [dbfread](https://dbfread.readthedocs.io/en/latest/exporting_data.html#dataset-sql).

<!--more-->

{{< code "/components/pglink" >}}

## Download a sample dbf file

We can get a sample `.dbf` file from [Infused](https://github.com/infused/dbf/blob/master/spec/fixtures/cp1251.dbf) with the following in our terminal

```bash
wget https://github.com/infused/dbf/raw/master/spec/fixtures/cp1251.dbf
```

This will store a file named `cp1251.dbf` in the current folder.

## Use dbfread to move the data into PostgreSQL 

We need to install simpledf with

```bash
pip install dbfread
```

Then we can write a Python script (named `convert_bdf_to_sql.py`) that opens the `sample.dbf` file and creates the PostgreSQL DDL and loads the data into a `CSV` file we can use to populate the database

```python
from dbfread import DBF
import dataset

db = dataset.connect('postgresql://[USER]:[PWD]@[HOST]:[PORT]/[DBNAME]?sslmode=require')
table = db['people']

for record in DBF('cp1251.dbf', lowernames=True):
    table.insert(record)
```

In the above script we:
* connect to a PostgreSQL instance using 
    * `[USER]`: the username
    * `[PWD]`: the password
    * `[HOST]`: the hostname
    * `[PORT]`: the port
    * `[DBNAME]`: the database name
* define a table named `people` that will be created and populated
* insert into the `people` table all the records in `cp1251.dbf`

We can then execute it with:

```bash
python convert_bdf_to_sql.py
```

If we now connect to our PostgreSQL database:

```bash
psql postgres://[USER]:[PWD]@[HOST]:[PORT]/[DBNAME]?sslmode=require
```

we can check the `people` table being populated with:

```sql
select * from people;
```

We can see the data in the table!

```
 id | rn |                  name
----+----+----------------------------------------
  1 |  1 | амбулаторно-поликлиническое
  2 |  2 | больничное
  3 |  3 | НИИ
  4 |  4 | образовательное медицинское учреждение
(4 rows)
```