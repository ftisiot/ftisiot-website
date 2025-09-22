---
title: 'How to tabulate a JSON to a recordset in PostgreSQL®'
date: "2023-01-12T15:06:50+01:00"
url: "/postgresqljson/how-to-tabulate-json-document-to-a-recordset-postgresql"
description: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
featured_image: "/images/2023/pg-json.png"
show_reading_time: true
categories:
- postgresql
- json
- jsonb
- remove
- fields
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, and provides two different ways to tabulate a JSON to a recordset:

<!--more-->

* the `json_populate_recordset` (`jsonb_populate_recordset` for JSONB) function using an existing database type
* the `json_to_recordset` (`jsonb_to_recordset` for JSONB) function defining the list of fields

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql/).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)



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

## Tabulate a JSON to a recordset using an existing database type with the `json_populate_recordset` function

To tabulate a JSON document to a table record using an existing database type you can use the `json_populate_recordset` (`jsonb_populate_recordset` in the example for JSONB column). To tabulate the JSON document:

* Create a dedicated database type

```
create type pizza as ("pizzaName" text, "additionalToppings" text[]);
```

* Tabulate the JSON using the dedicated function

```
select p.* from test
cross join lateral 
    jsonb_populate_recordset(
        null::pizza, json_data -> 'pizzas') p;
```

Result

```
 pizzaName  | additionalToppings
------------+--------------------
 Salami     | {🥓,🌶️}
 Margherita | {🍌,🌶️,🍍}
```

## Tabulate a JSON to a recordset defining the list of fields with the `json_to_recordset` function

To tabulate a JSON document to a table recordset defining the list of fields you can use the `json_to_recordset` (`jsonb_to_recordset` in the example for JSONB column). To tabulate the JSON document:

```
select p.* from test
cross join lateral 
    jsonb_to_recordset(json_data -> 'pizzas') 
    as p("pizzaName" text, "additionalToppings" text[]);
```

Result

```
 pizzaName  | additionalToppings
------------+--------------------
 Salami     | {🥓,🌶️}
 Margherita | {🍌,🌶️,🍍}
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main/)