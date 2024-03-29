---
title: 'How to insert a field in a JSON document in MySQL'
date: "2023-07-21T16:53:52+02:00"
url: "/mysqljson/how-to-insert-field-JSON-document-mysql"
description: "How to insert a field in a JSON document in MySQL"
tldr: "How to insert a field in a JSON document in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
featured_image: "/images/2023/mysql-json-little.png"
show_reading_time: true
categories:
- mysql
- json
- insert
---


You can insert a field in a JSON document in MySQL with the `JSON_INSERT` function. 
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

## Insert a field in a JSON document with `JSON_INSERT` function

To insert a field in a JSON document you can use the `JSON_INSERT` function. To insert the `drinks` in the second pizza in the above order you can:

```sql
select 
    JSON_INSERT(json_data,'$.pizzas[1].drinks','Sparkling Water') extra_drink from test;
```

Where
* `json_data` is the JSON column 
* `$.pizzas[1].drink` selects the `pizzas` item, the 2nd element in the array and the `drink` subitem
* the last parameter `Sparkling Water` represents the value to replace

Result

```
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| extra_drink                                                                                                                                                                                                                                                                                                                                                                 |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"drinks": "Sparkling Water", "pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056 DPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}                         |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)
