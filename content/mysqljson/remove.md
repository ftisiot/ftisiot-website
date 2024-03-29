---
title: 'How to remove a field in a JSON document in MySQL'
date: "2023-07-21T17:01:19+02:00"
url: "/mysqljson/how-to-remove-field-JSON-document-mysql"
description: "How to remove a field in a JSON document in MySQL"
tldr: "How to remove a field in a JSON document in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
featured_image: "/images/2023/mysql-json-little.png"
show_reading_time: true
categories:
- mysql
- json
- remove
---


You can remove a field from JSON document in MySQL with the `JSON_REMOVE` function.

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

## Remove a field from a JSON document with `JSON_REMOVE` function

To remove one or more fields from a JSON document you can use the `JSON_REMOVE` function. To remove the the `pizzaName` from the second pizza in the above order you can:

```sql
select 
    JSON_REMOVE(json_data,'$.pizzas[1].pizzaName') remove_pizzaName from test;
```

Where
* `json_data` is the JSON column 
* `$.pizzas[1].pizzaName` selects the `pizzas` item, the 2nd element in the array and the `pizzaName` subitem

Result

```
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| remove_pizzaName                                                                                                                                                                                                                                                                                                    |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056 DPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}                         |
+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)