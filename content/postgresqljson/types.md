---
title: 'How to get the JSON field types in PostgreSQL®?'
date: "2023-01-06T16:59:12+01:00"
url: "/postgresqljson/how-to-get-json-field-types-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
featured_image: "/images/2023/pg-json.png"
show_reading_time: true
categories:
- postgresql
- json
- jsonb
- types
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use the function `json_typeof` (`jsonb_typeof` for `JSONB`) to extract the fields type.

<!--more-->

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

{{< code "/components/pglink" >}}

## The dataset

The dataset is the following:

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

<details>
  <summary>Check out the description of the fields</summary>
The following examples use a pizza order dataset with an order having:

* `id`: 778
* `shop`: "Luigis Pizza"
* `name`: "Edward Olson"
* `phoneNumbers`:["(935)503-3765x4154","(935)12345"]
* `address`: "Unit 9398 Box 2056\nDPO AP 24022"
* `image`: null
* and two pizzas contained in the `pizzas` item:

```
[
    {
        "pizzaName": "Salami",
        "additionalToppings": ["🥓", "🌶️"]
    },
    {
        "pizzaName": "Margherita",
        "additionalToppings": ["🍌", "🌶️", "🍍"]
    }
]
```
</details>
<details>
  <summary>If you want to reproduce the examples, check how to recreate the dataset</summary>

It can be recreated with the following script:

```
create table test(id serial, json_data jsonb);

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

</details>

## Extract a JSON field type using the `json_typeof` function

The field type can be extracted using the `json_typeof` (`jsonb_typeof` for `JSONB`) function

Example

```
select 
    jsonb_typeof(json_data->'id') type_id,
    jsonb_typeof(json_data->'name') type_name
from 
    test;
```

Results

```
 type_id | type_name
---------+-----------
 number  | string
```

The `->` operator to extract a JSON field can be reviewed in the [dedicated document](/postgresqljson/how-to-extract-field-from-json-postgresql)

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)