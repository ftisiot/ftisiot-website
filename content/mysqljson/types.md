---
title: 'How to get the JSON field types in MySQL'
date: "2023-07-21T14:59:42+02:00"
url: "/mysqljson/how-to-get-json-field-types-mysql"
description: "How to get the JSON field types in MySQL"
tldr: "How to get the JSON field types in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
featured_image: "/images/2023/mysql-json-little.png"
show_reading_time: true
categories:
- mysql
- json
- extract
- types
---

To get the type of a JSON item in MySQL you need to use the `JSON_TYPE` function.

<!--more-->

> **NOTE**: more info is available in the [MySQL JSON functions documentation page](https://dev.mysql.com/doc/refman/8.0/en/json.html)

{{< code "/components/mysqllink" >}}


## The dataset

The dataset is the following:

```json
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

```json
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

```sql
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

## Extract a JSON field type using the `JSON_TYPE` function

You can get the type of JSON data with the `JSON_TYPE` function

```sql
select 
    JSON_TYPE(json_data -> '$.pizzas') pizzas
    from test;
```

In the above query

* `json_data -> '$.pizzas'` extracts the `pizzas` field
* the surrounding `JSON_TYPE` retrieves the type

Result

```
+--------+
| pizzas |
+--------+
| ARRAY  |
+--------+
```

Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)