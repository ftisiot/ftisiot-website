---
title: 'How to extract an item from an array in a JSON object in MySQL'
date: "2023-07-21T14:52:34+02:00"
url: "mysqljson/how-to-extract-field-from-array-json-mysql"
description: "How to extract an item from an array in a JSON object in MySQL"
tldr: "How to extract an item from an array in a JSON object in MySQL"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
thumbnail: "/images/2023/mysql-json-little.png"
categories:
- mysql
- json
- extract
- array
---



To extract an item from an array in MySQL you need to use the `->` operator and the `[item_number]`  [JSON Path Syntax](https://dev.mysql.com/doc/refman/8.0/en/json.html#json-path-syntax).

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

## Extract an item from a JSON array with the `->` operator

Using the `->` operator in conjunction with the `[item_number]` JSON Path sintax, we can extract from an array. The `[item_number]` syntax follows the JSON standards, therefore the array starting index is `0`. The second pizza in the order can be extracted with

```
select 
    json_data -> '$.pizzas[1]' second_pizza
    from test;
```

In the above query

* `json_data -> '$.pizzas'` extracts the `pizzas` field
* the additional `[1]` extracts the second pizza (index starts from `0`)

Result

```
+--------------------------------------------------------------------------------+
| second_pizza                                                                   |
+--------------------------------------------------------------------------------+
| {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}          |
+--------------------------------------------------------------------------------+
```




Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)