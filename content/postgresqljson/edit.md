---
title: 'How to edit a JSON object in PostgreSQL®?'
date: "2023-01-10T08:53:59+01:00"
url: "/postgresqljson/how-to-edit-json-postgresql"
description: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- edit
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use the `jsonb_set` function to remove the null values for JSONB columns.

<!--more-->


> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

<p style="border:2px dotted #77dd77;"> 👉 If you want to try it out on a FREE PostgreSQL database, check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a></p>

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

## Edit the JSON object with the `jsonb_set` function

JSON objects can be edited using the `jsonb_set` function. To change the first `pizzaName` in the `pizzas` item to `4 Stagioni`

```
select
    jsonb_set(
        json_data -> 'pizzas',
        '{0,"pizzaName"}',
        to_jsonb('4 Stagioni'::text), 
        false)
        as change_first_pizza_name
from test;
```

Result

```
                                                        change_first_pizza_name
----------------------------------------------------------------------------------------------------------------------------------------
 [{"pizzaName": "4 Stagioni", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}]
(1 row)
```

The `json_set` has the following parameters:

1. the JSON object to edit (`json_data -> 'pizzas'`)
2. the path to the key to be edited (`'{0,"pizzaName"}'`, `0` is the first pizza in the array, `pizzaName` is the key)
3. the new value (`to_jsonb('4 Stagioni'::text)`)
4. boolean to create field if missing (`false`)

> **NOTE**: The `jsonb_set_lax` function has the same output of `jsonb_set` for non null values. For null values, it changes the behaviour depending on a 5th parameter (`null_value_treatment`) that can be:
>  * `raise_exception`
>  * `use_json_null`
>  * `delete_key`
>  * `return_target`


Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)