---
title: 'How to create a JSON document from fields in MySQL'
date: "2023-07-21T15:21:29+02:00"
url: "/mysqljson/how-to-create-JSON-document-from-fields-mysql"
description: "How to create a JSON document from fields in MySQL"
tldr: "How to create a JSON document from fields in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
featured_image: "/images/2023/mysql-json-little.png"
show_reading_time: true
categories:
- mysql
- json
- create
---


You can create a JSON document from fields in Mysql® with the `JSON_OBJECT` function

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

## Create a JSON object with `JSON_OBJECT` function

To create a JSON document, from a list of fields or strings you can use the `JSON_OBJECT` function. 

```sql
select 
    JSON_OBJECT('mykey1','myvalue1','mykey2','myvalue2') json_data;
```

Result

```
+----------------------------------------------+
| json_data                                    |
+----------------------------------------------+
| {"mykey1": "myvalue1", "mykey2": "myvalue2"} |
+----------------------------------------------+
```


Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)