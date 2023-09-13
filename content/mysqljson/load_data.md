---
title: 'How to load JSON data in MySQL?'
date: "2023-07-21T14:24:56+02:00"
url: "/mysqljson/how-to-load-json-mysql"
description: "How to load JSON data in MySQL?"
tldr: "How to load JSON data in MySQL?"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
thumbnail: "/images/2023/mysql-json-little.png"
categories:
- mysql
- json
- load
---


To load JSON data into a MySQL column you need to include it as string. 

<!--more-->

E.g. to include the following JSON:

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

In a table containing a `json_data` `JSON` column:

```
create table test(id serial primary key, json_data json);
```

You can just insert the `json_data` column as string:

```
insert into test(id, json_data) values (
1, 
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

MySQL will validate the content as JSON and, if correct, store the data. 

If you retrieve the data from the `test` table with

```
select * from test;
```

You can see the row correctly stored.

```
+----+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | json_data                                                                                                                                                                                                                                                                                                                                      |
+----+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|  1 | {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056 DPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}                         |
+----+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.07 sec)
```




<p style="background: #cccccc;border: 1px solid #666666;border-radius: 15px;text-align: center;">👉 Need a <b>FREE</b> MySQL database? check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a>👈</p>

> **NOTE**: more info is available in the [MySQL JSON functions documentation page](https://dev.mysql.com/doc/refman/8.0/en/json.html)