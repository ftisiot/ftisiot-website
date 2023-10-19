---
title: 'How to remove fields from a JSON document in PostgreSQL®?'
date: "2023-01-10T08:53:59+01:00"
url: "/postgresqljson/how-to-remove-fields-from-json-postgresql"
description: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- remove
- fields
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, and provides two different ways to remove fields from a JSON document:

<!--more-->

* the `-` operator to remove first level fields
* the `#-` operator to remove specific path

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

## Remove first level fields from JSON with the `-` operator

First level fields can be removed from a JSON document using the `-` operator. To remove the `pizzas` and `id` fields from the pizza order

```
select 
    json_data 
        - ARRAY['pizzas','id']
        as no_pizzas_and_id
from test;
```

Result

```
                                                                           no_pizzas_and_id
----------------------------------------------------------------------------------------------------------------------------------------------------------------------
 {"name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "address": "Unit 9398 Box 2056\nDPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}
(1 row)
```

## Remove specific path fields from JSON with the `-#` operator

Specific path fields can be removed from a JSON document using the `-#` operator. To remove the `additionalToppings` from the second pizza (index starts from `0`) in the `pizzas` item

```
select 
    json_data 
       #- '{pizzas,1,additionalToppings}'
       as no_2nd_pizza_additionalToppings
from test;

```

Result

```
                                                                                                                           no_2nd_pizza_additionaltoppings
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita"}], "address": "Unit 9398 Box 2056\nDPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}
(1 row)
```

The `'{pizzas,1,additionalToppings}'` is a JSON path identifying the `pizzas` field, the 2nd entry in the array (`1` since the index starts from `0`) and the nested `additionalToppings` field.

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)