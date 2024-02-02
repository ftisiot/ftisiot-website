---
title: 'How to load JSON data in PostgreSQL®?'
date: "2023-03-01T07:59:12+01:00"
url: "/postgresqljson/how-to-load-json-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- index
- query
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`. This doc showcases how to load JSON data into a  column.

<!--more-->

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)



A quick overview of the methods is available in the video, scroll further for the written examples. 

{{< youtube NgIdO4FkC_A >}} 

{{< code "/components/pglink" >}}

If you need to load JSON files in an existing table with well defined columns, check the dedicated article on [how to load the JSON Data with PostgreSQL COPY and jq](/postgresqljson/load-json-data-with-postgresql-copy).

## Load JSON data in PostgreSQL

The load a JSON document in a `JSON` or `JSONB` column in PostgreSQL you need to include it as string. E.g. to include the following JSON:

```
{
    "id": 778,
    "shop": "Luigis Pizza",
    "name": "Edward Olson",
    "phoneNumbers":
        ["(935)503-3765x4154","(935)12345"],
    "address": "Unit 9398 Box 2056\nDPO AP 24022",
    "image": null,
    "pizzas": [
        {
            "pizzaName": "Salami",
            "additionalToppings": ["🥓", "🌶️"]
        },
        {
            "pizzaName": "Margherita",
            "additionalToppings": ["🍌", "🌶️", "🍍"]
        }
    ]
}
```

In a table containing a `json_data` `JSONB` column:

```
create table test(id serial, json_data jsonb);
```

You can just insert the `json_data` column as string:

```
insert into test(json_data) values (
'{
    "id": 778,
    "shop": "Luigis Pizza",
    "name": "Edward Olson",
    "phoneNumbers":
        ["(935)503-3765x4154","(935)12345"],
    "address": "Unit 9398 Box 2056\nDPO AP 24022",
    "image": null,
    "pizzas": [
        {
            "pizzaName": "Salami",
            "additionalToppings": ["🥓", "🌶️"]
        },
        {
            "pizzaName": "Margherita",
            "additionalToppings": ["🍌", "🌶️", "🍍"]
        }
    ]
}');
```

PostgreSQL will validate the content as JSON and, if correct, store the data.

To check an example of how to load a set of JSON in a PostgreSQL table, check the [Indian restaurant story](https://aiven.io/blog/pg-json-indian-restaurants)

> **NOTE**: If you directly want to load a file with the `\copy` command, containing any `\` characters, check out the [StackOverflow thread](https://stackoverflow.com/questions/43855930/postgresql-json-to-columns-error-character-with-value-must-be-escaped).   
>  TLDR:
>```
>\copy <TABLE_NAME> from program 'sed -e ''s/\\/\\\\/g'' <FILE_NAME>.json';
>```
