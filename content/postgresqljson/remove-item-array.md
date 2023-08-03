---
title: 'How to remove an item from a JSON array in PostgreSQL®?'
date: "2023-01-10T08:53:59+01:00"
url: "/postgresqljson/how-to-remove-items-from-json-array-postgresql"
description: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- remove
- items
- array
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use the `-` operator to remove an item from an array.

<!--more-->


> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

<p style="background: #cccccc;border: 1px solid #666666;border-radius: 15px;text-align: center;">👉 Need a <b>FREE</b> PostgreSQL database? check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a>👈</p>

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

## Remove an item from a JSON array with the `-` operator

Items in a JSON array can be removed using the `-` operator. To remove the second items within the `pizzas` array

```
select 
    (json_data -> 'pizzas') - 1
        as no_second_pizza
from test;
```

Result

```
                       no_second_pizza
--------------------------------------------------------------
 [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}]
(1 row)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)