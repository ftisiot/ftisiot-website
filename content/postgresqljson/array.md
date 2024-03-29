---
title: 'How to parse JSON arrays in PostgreSQL?'
date: "2023-01-09T11:33:09+01:00"
url: "/postgresqljson/how-to-parse-json-arrays-in-postgresql"
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
- parse
- array
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, and provides several different ways to parse arrays from a JSON document:

<!--more-->

* the `json_array_elements` (`jsonb_array_elements` for `JSONB` columns) function to extract the array elements as JSON objects
* the `json_array_elements_text` (`jsonb_array_elements_text` for `JSONB` columns) function to extract the array elements as text
* the `json_array_length` (`jsonb_array_length` for `JSONB` columns) function to retrieve the length of the array

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

## Extract the JSON array elements as JSON objects with the `json_array_elements` function

The `pizzas` items can be extracted creating one row per element using the `json_array_elements` (`jsonb_array_elements` since the column is defined as JSONB) function:

```
select p.*
from test 
cross join lateral jsonb_array_elements(
    json_data -> 'pizzas') p;
```

Result

```
                                value
----------------------------------------------------------------------
 {"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}
 {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}
(2 rows)
```

## Extract the JSON array elements as Text objects with the `json_array_elements_text` function

The `phoneNumbers` items can be extracted creating one row per element using the `json_array_elements_text` (`jsonb_array_elements_text` since the column is defined as JSONB) function:

```
select p.*
from test 
cross join lateral jsonb_array_elements_text(
    json_data -> 'phoneNumbers') p;
```

Result

```
       value
--------------------
 (935)503-3765x4154
 (935)12345
(2 rows)
```

## Retrieve the legnth of the JSON array with the `json_array_length` function

The `phoneNumbers` array length can be retrieved using the `json_array_length` (`jsonb_array_length` since the column is defined as JSONB) function:

```
select 
    jsonb_array_length(
json_data -> 'phoneNumbers')
from test;
```

Result

```
 jsonb_array_length
--------------------
                  2
(1 row)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)