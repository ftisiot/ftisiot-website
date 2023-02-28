---
title: 'How to extract a field from a JSON object in PostgreSQL®?'
date: "2023-01-06T16:59:12+01:00"
url: "/postgresqljson/how-to-extract-field-from-json-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- extract
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, and provides several different ways to extract fields from a JSON document:

<!--more-->

* the `->` operator to extract the field as JSON
* the `->` operator to extract an item from an array
* the `->>` operator to extract the field as text
* the `#>` operator to extract the object at a specific path as JSON
* the `#>>` operator to extract the object at a specific path as text
* the `[]` operator to extract the object at a specific path as JSON
* the `json_extract_path` (`jsonb_extract_path` for `JSONB` columns) function to extract the object at a specific path as JSON

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

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

## Extract a JSON field using the `->` operator

To extract a field from a JSON document, you can use the `->` operator. The `id` and `name` fields can be extracted with:

```
select 
    json_data -> 'id' id,
    json_data -> 'name' name
    from test;
```

Result

```
 id  |      name
-----+----------------
 778 | "Edward Olson"
 ```

## Extract an item from a JSON array with the `->` operator

The `->` operator works with:

* **text** to identify a field name
* **integers** to identify an item within an array

Therefore the second pizza in the order can be extracted with

```
select 
    json_data -> 'pizzas' -> 1 as second_pizza
    from test;
```

In the above query

* `json_data -> 'pizzas'` extracts the `pizzas` field
* the additional `-> 1` extracts the second pizza (index starts from `0`)

Result

```
                             second_pizza
----------------------------------------------------------------------
 {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}
(1 row)
```


## Extract a JSON field as Text using the `->>` operator

To extract a field from a JSON document as Text, you can use the `->` operator. The `id` and `name` fields can be extracted with:

```
select 
    json_data ->> 'id' id,
    json_data ->> 'name' order_name
from test;
```

Result:

```
 id  |  order_name
-----+--------------
 778 | Edward Olson
 ```

## Extract a JSON field as a specific path using the `@>` operator

The `->` operator allows only to extract "first level" fields, to extract in only one pass more nested items you can use the `@>` operator. 

E.g. to extract the `additionalToppings` belonging to the second pizza in the order:

```
select 
    json_data #> 
       '{pizzas,1,additionalToppings}'  
        as additional_toppings_2nd_pizza
from test;
```

The `{pizzas,1,additionalToppings}` is the concatenation of:

* `pizzas`: the field name
* `1`: the second item in the `pizzas` array (index starts from `0`)
* `additionalToppings`: the field name within the `pizzas`

Results

```
 additional_toppings_2nd_pizza
-------------------------------
 ["🍌", "🌶️", "🍍"]
(1 row)
```
## Extract a JSON field as a specific path using the `[]` operator (subscripting)

To get the second pizza name in the order you can use the `[]` operator

```
select 
    json_data['pizzas']['1']['pizzaName'] second_pizza_name
from test;
```

Result

```
 second_pizza_name
-------------------
 "Margherita"
(1 row)
```

## Extract a JSON field as a specific path using the `json_extract_path` function

To get the second pizza name in the order you can use the `json_extract_path` (`jsonb_extract_path` since the column is defined as JSONB)

```
select 
    jsonb_extract_path(json_data, 'pizzas', '1', 'pizzaName') second_pizza_name
from test;
```

Result

```
 second_pizza_name
-------------------
 "Margherita"
(1 row)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)