---
title: 'How to edit a JSON document in MySQL'
date: "2023-07-21T16:35:20+02:00"
url: "/mysqljson/how-to-edit-JSON-document-mysql"
description: "How to edit a JSON document in MySQL"
tldr: "How to edit a JSON document in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
thumbnail: "/images/2023/mysql-json-little.png"
categories:
- mysql
- json
- edit
---


You can edit a JSON document in MySQL with:

* The `JSON_SET` function that will replace values for JSON paths that exists and add values for the ones that don't exist.
* The `JSON_REPLACE` function that will replace values for JSON paths that exists and ignore the ones that don't exist.

<!--more-->

> **NOTE**: more info is available in the [MySQL JSON functions documentation page](https://dev.mysql.com/doc/refman/8.0/en/json.html)

<p style="border:2px dotted #77dd77;"> 👉 Need a <b>FREE</b> MySQL database? check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a></p>


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

## Edit a JSON document with `JSON_SET` function

To edit a JSON document you can use the `JSON_SET` function. To replace the second `pizzaName` from `Margherita` to `Capricciosa` you can

```
select 
    JSON_SET(json_data,'$.pizzas[1].pizzaName','Capricciosa') from test;
```

Where
* `json_data` is the JSON column 
* `$.pizzas[1].pizzaName` selects the `pizzas` item, the 2nd element in the array and the `pizzaName` subitem
* the last parameter `Capricciosa` represents the value to replace

Result

```
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Capricciosa", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056 DPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}                         |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

## Edit a JSON document with `JSON_REPLACE` function

To edit a JSON document you can use the `JSON_REPLACE` function. To replace the second `pizzaName` from `Margherita` to `Capricciosa` you can

```
select 
    JSON_REPLACE(json_data,'$.pizzas[1].pizzaName','Capricciosa') from test;
```

Where
* `json_data` is the JSON column 
* `$.pizzas[1].pizzaName` selects the `pizzas` item, the 2nd element in the array and the `pizzaName` subitem
* the last parameter `Capricciosa` represents the value to replace

Result

```
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| JSON_REPLACE(json_data,'$.pizzas[1].pizzaName','Capricciosa')                                                                                                                                                                                                                                                                                   |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Capricciosa", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056 DPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}                         |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

**Note**: If you try to use the `JSON_REPLACE` with a non existing field, like the `$.nameS`, the function will **NOT** edit the document.

```
select 
    JSON_REPLACE(json_data,'$.nameS','Ugo') from test;
```

The above doesn't edit the document since the key `nameS` is not present

```
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| JSON_REPLACE(json_data,'$.nameS','Ugo')                                                                                                                                                                                                                                                                                                        |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056 DPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}                         |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)