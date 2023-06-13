---
title: 'How to tabulate a JSON to a record in PostgreSQL®'
date: "2023-01-12T15:06:50+01:00"
url: "/postgresqljson/how-to-tabulate-json-document-to-a-record-postgresql"
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

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, and provides two different ways to tabulate a JSON to a record:

<!--more-->

* the `json_populate_record` (`jsonb_populate_record` for JSONB) function using an existing database type
* the `json_to_record` (`jsonb_to_record` for JSONB) function defining the list of fields

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

## Tabulate a JSON to a record using an existing database type with the `json_populate_record` function

To tabulate a JSON document to a table record using an existing database type you can use the `json_populate_record` (`jsonb_populate_record` in the example for JSONB column). To tabulate the JSON document:

* Create a dedicated database type

```
create type pizzaOrder as (id int, name text, address text);
```

* Tabulate the JSON using the dedicated function

```
select p.* 
from test cross join lateral 
    jsonb_populate_record(
        null::pizzaOrder, json_data) p;
```

Result

```
 id  |     name     |      address
-----+--------------+--------------------
 778 | Edward Olson | Unit 9398 Box 2056+
     |              | DPO AP 24022
```

## Tabulate a JSON to a record defining the list of fields with the `json_to_record` function

To tabulate a JSON document to a table record defining the list of fields you can use the `json_to_record` (`jsonb_to_record` in the example for JSONB column). To tabulate the JSON document:

```
select p.* from test
cross join lateral 
    jsonb_to_record(json_data) 
    as p(id int, "phoneNumbers" text[]);
```

Result

```
 id  |          phoneNumbers
-----+---------------------------------
 778 | {(935)503-3765x4154,(935)12345}
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)