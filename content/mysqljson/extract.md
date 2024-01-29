---
title: 'How to extract a field from a JSON object in MySQL'
date: "2023-07-21T14:36:10+02:00"
url: "/mysqljson/how-to-extract-field-from-json-mysql"
description: "How to extract a field from a JSON object in MySQL"
tldr: "How to extract a field from a JSON object in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
thumbnail: "/images/2023/mysql-json-little.png"
categories:
- mysql
- json
- extract
- JSON_EXTRACT
---


MySQL® offers three ways to extract fields from a JSON object:

* the `->` operator to extract the field as JSON
* the `->>` operator to extract the field as text
* the `JSON_EXTRACT` function

Both operators use the [JSON Path Syntax](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-path-syntax)

<!--more-->

> **NOTE**: more info is available in the [MySQL JSON functions documentation page](https://dev.mysql.com/doc/refman/8.0/en/json.html)

{{< code "/components/mysqllink" >}}


## The dataset

The dataset is the following:

```
{
    "id": 778,
    "shop": "Luigis Pizza",
    "name": "Edward Olson",
    "phoneNumbers":
        ["(935)503-3765x4154","(935)12345"],
    "address": "Unit 9398 Box 2056 DPO AP 24022",
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
* `address`: "Unit 9398 Box 2056 DPO AP 24022"
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
create table test(id serial primary key, json_data json);

insert into test(json_data) values (
'{
    "id": 778,
    "shop": "Luigis Pizza",
    "name": "Edward Olson",
    "phoneNumbers":
        ["(935)503-3765x4154","(935)12345"],
    "address": "Unit 9398 Box 2056 DPO AP 24022",
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

To extract a field value from a JSON document, you can use the `->` operator. The `id` and `name` fields can be extracted with:

```
select 
    json_data -> '$.id' id,
    json_data -> '$.name' name
    from test;
```

Result

```
+------+----------------+
| id   | name           |
+------+----------------+
| 778  | "Edward Olson" |
+------+----------------+
```


## Extract a JSON field as Text using the `->>` operator

To extract a field from a JSON document as Text, you can use the `->` operator. The `id` and `name` fields can be extracted with:

```
select 
    json_data ->> '$.id' id,
    json_data ->> '$.name' order_name
from test;
```

Result:

```
+------+--------------+
| id   | order_name   |
+------+--------------+
| 778  | Edward Olson |
+------+--------------+
 ```

## Extract a JSON field as using the `JSON_EXTRACT` function

To extract a field from a JSON document, you can use the `JSON_EXTRACT` function. The `id` and `name` fields can be extracted with:

```
select 
    JSON_EXTRACT(json_data, '$.id') id,
    JSON_EXTRACT(json_data, '$.name') order_name
from test;
```

Result:

```
+------+----------------+
| id   | order_name     |
+------+----------------+
| 778  | "Edward Olson" |
+------+----------------+
 ```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)