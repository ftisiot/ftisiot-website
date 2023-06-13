---
title: 'How to parse JSON keys in PostgreSQL®'
date: "2023-01-09T16:58:02+01:00"
url: "/postgresqljson/how-to-parse-json-keys-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- keys
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use several functions to extract the keys and related values in tabular format.

<!--more-->

* `json_each` (`jsonb_each` for the JSONB type) to extract the key as text and the value as separate JSON object
* `json_each_text` (`jsonb_each_text` for the JSONB type) to extract the key as text and the value as separate text object
* `json_object_keys` (`jsonb_object_keys` for the JSONB type) to extract the keys

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

> ℹ️ If you want to try it out on a FREE PostgreSQL database, check [Aiven's free plans](https://console.aiven.io/signup)

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

## Extract the key as text and the value as JSON object with the `json_each` function

The keys and related values can be extracted with the `json_each` (`jsonb_each` since the column is defined as JSONB) function

```
select p.*
from test 
cross join lateral jsonb_each(json_data) p;
```

Result

```
     key      |                                                               value
--------------+------------------------------------------------------------------------------------------------------------------------------------
 id           | 778
 name         | "Edward Olson"
 shop         | "Luigis Pizza"
 image        | null
 pizzas       | [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}]
 address      | "Unit 9398 Box 2056\nDPO AP 24022"
 phoneNumbers | ["(935)503-3765x4154", "(935)12345"]
(7 rows)
```

## Extract the key as text and the value as text object with the `json_each_text` function

The keys and related values can be extracted with the `json_each_text` (`jsonb_each_text` since the column is defined as JSONB) function

```
select p.*
from test 
cross join lateral jsonb_each_text(json_data) p;
```

Result

```
     key      |                                                               value
--------------+------------------------------------------------------------------------------------------------------------------------------------
 id           | 778
 name         | Edward Olson
 shop         | Luigis Pizza
 image        | 👻
 pizzas       | [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}]
 address      | Unit 9398 Box 2056                                                                                                                +
              | DPO AP 24022
 phoneNumbers | ["(935)503-3765x4154", "(935)12345"]
(7 rows)
```

> **NOTE**: compared to `json_each`, the `json_each_text` sets the output of the `value` column as text rather than JSON (or JSONB)

## Extract the JSON keys with the `json_object_keys` function

The keys can be extracted with the `json_object_keys` (`jsonb_object_keys` since the column is defined as JSONB) function

```
select 
    jsonb_object_keys(json_data) 
from test;
```

Result

```
 jsonb_object_keys
-------------------
 id
 name
 shop
 image
 pizzas
 address
 phoneNumbers
(7 rows)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)