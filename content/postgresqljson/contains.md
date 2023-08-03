---
title: 'How to check if JSON contains in PostgreSQL®?'
date: "2023-01-06T16:39:26+01:00"
url: "/postgresqljson/how-to-check-JSON-contains-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- contains
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, and provides several different ways to check if a value/field from a JSON document:

<!--more-->

* the `@>` operator to check if a JSON is contained
* the `?` operator to check if a field name is contained
* the `?|` operator to check if any field name in the array is contained
* the `?&` operator to check if all the field names in the array are contained

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

<p style="border:2px dotted #77dd77;"> 👉 Need a **FREE** PostgreSQL database? check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a></p>

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

## Check if a JSON is contained with the `@>` operator

To check if a JSON object `{"id": 778}` is contained, you can use

```
select 
    json_data 
        @> '{"id": 778}'::jsonb  
        as contains_id
from test;
```

Result

```
 contains_id
-------------
 t
(1 row)
```

## Check if a field name is contained with the `?` operator

To check if the `shop` field

```
select 
    json_data ? 'shop'
        as contains_shop
from test;
```

Result

```
 contains_shop
---------------
 t
(1 row)
```


## Check if any field name in the array is contained with the `?|` operator


To check if the JSON object contains any of the `shop` and `cookies` fields

```
select 
    json_data 
        ?| ARRAY['shop','cookies']
        as contains_shop_or_cookies
from test;
```

Result

```
 contains_shop_or_cookies
--------------------------
 t
(1 row)
```

## Check if all the field names in the array are contained the `?&` operator

To check if the JSON object contains both the `shop` and `cookies` fields

```
select 
    json_data 
        ?& ARRAY['shop','cookies']
        as contains_shop_or_cookies
from test;
```

Result

```
 contains_shop_or_cookies
--------------------------
 f
(1 row)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)