---
title: 'How to merge JSON documents in MySQL'
date: "2023-07-21T15:31:08+02:00"
url: "/mysqljson/how-to-merge-JSON-documents-mysql"
description: "How to merge JSON documents in MySQL"
tldr: "How to merge JSON documents in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
thumbnail: "/images/2023/mysql-json-little.png"
categories:
- mysql
- json
- merge
---


You can merge two JSON documents in MySQL with:

* the `JSON_MERGE_PRESERVE` function to concatenate the document values
* the `JSON_MERGE_PATCH` function to keep the latest value for each key

<!--more-->

> **NOTE**: more info is available in the [MySQL JSON functions documentation page](https://dev.mysql.com/doc/refman/8.0/en/json.html)

<p style="background: #cccccc;border: 1px solid #666666;border-radius: 15px;text-align: center;">👉 Need a <b>FREE</b> MySQL database? check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a>👈</p>


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

## Merge two JSON documents with `JSON_MERGE_PRESERVE` function

To merge two (or more) JSON documents, by concatenating all the key values you can use the `JSON_MERGE_PRESERVE` function. 

```
select 
    JSON_MERGE_PRESERVE(
        '{"mykey1":"myvalue11","mykey2":"myvalue21"}',
        '{"mykey1":"myvalue12","mykey2":"myvalue22"}'
        ) json_data;
```

Result

```
+------------------------------------------------------------------------------+
| json_data                                                                    |
+------------------------------------------------------------------------------+
| {"mykey1": ["myvalue11", "myvalue12"], "mykey2": ["myvalue21", "myvalue22"]} |
+------------------------------------------------------------------------------+
```

## Merge two JSON documents with `JSON_MERGE_PATCH` function

To merge two (or more) JSON documents, by keeping only the latest value for each key you can use the `JSON_MERGE_PATCH` function. 

```
select 
    JSON_MERGE_PATCH(
        '{"mykey1":"myvalue11","mykey2":"myvalue21"}',
        '{"mykey1":"myvalue12","mykey2":"myvalue22"}'
        ) json_data;
```

Result

```
+------------------------------------------------+
| json_data                                      |
+------------------------------------------------+
| {"mykey1": "myvalue12", "mykey2": "myvalue22"} |
+------------------------------------------------+
```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)