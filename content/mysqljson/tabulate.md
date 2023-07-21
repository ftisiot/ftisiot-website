---
title: 'How to tabulate a JSON document in MySQL'
date: "2023-07-21T17:05:26+02:00"
url: "/mysqljson/how-to-tabulate-JSON-document-mysql"
description: "How to tabulate a JSON document in MySQL"
tldr: "How to tabulate a JSON document in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
thumbnail: "/images/2023/mysql-json-little.png"
categories:
- mysql
- json
- tabulate
---

You can tabulate a JSON document (retrieve it as a row) in MySQL with the `JSON_TABLE` function.

<!--more-->

> **NOTE**: more info is available in the [MySQL JSON functions documentation page](https://dev.mysql.com/doc/refman/8.0/en/json.html)

<p style="border:2px dotted #77dd77;"> 👉 If you want to try it out on a FREE MySQL database, check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a></p>


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

## Tabulate a JSON document with `JSON_TABLE` function

To retrieve one or more JSON documents as columns and rows you can use the `JSON_TABLE` function. To retrieve the list of pizzas and their first additional topping from the above as table you can:

```
select tbl.* from
    test, 
    JSON_TABLE(
        json_data,
        '$.pizzas[*]' 
        COLUMNS (
            pizzaName VARCHAR(100) PATH '$.pizzaName', 
            additionalToppings VARCHAR(100) PATH '$.additionalToppings[0]'
            )
        ) tbl;
```

Where
* `json_data` is the JSON column 
* `'$.pizzas[*]'` generates a row for each pizza in the `pizzas` array
* `pizzaName VARCHAR(100) PATH '$.pizzaName'` retrieves the `pizzaName` field
* `additionalToppings VARCHAR(100) PATH '$.additionalToppings[0]'` retrieves the first element (`[0]`) of the `additionalToppings` array

Result

```
+------------+--------------------+
| pizzaName  | additionalToppings |
+------------+--------------------+
| Salami     | 🥓                 |
| Margherita | 🍌                 |
+------------+--------------------+
```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)