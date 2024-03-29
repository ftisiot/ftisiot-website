---
title: 'How to query JSON in MySQL with JSON_CONTAINS'
date: "2024-03-01T10:53:23+01:00"
url: "/mysqljson/query-json-contains-mysql"
description: "How to query JSON in MySQL with json_contains"
tldr: "How to query JSON in MySQL with json_contains"
image: "/images/2023/mysql-json.png"
credit: "ftisiot"
featured_image: "/images/2023/mysql-json-little.png"
show_reading_time: true
categories:
- mysql
- json
- contains
- json_contains
- json_contains_path
---


You can query a JSON document in MySQL to find content within it with:

* The `JSON_CONTAINS` function that will return if a JSON document is contained within another JSON document. The function returns `1` if the document is contained, `0` elsewhere.
* The `JSON_CONTAINS` function that will return if a JSON document path is contained within another JSON document. The function returns `1` if the document is contained, `0` elsewhere.

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

## Query JSON in MySQL with JSON_CONTAINS

To query a JSON document you can use the `JSON_CONTAINS` function. To find if the data in contains a `pizzaName` equal to `Margherita` you can:

```sql
select 
    JSON_CONTAINS(json_data,'{"pizzaName":"Margherita"}', '$.pizzas') result from test;
```

Where
* `json_data` is the JSON column 
* `{"pizzaName":"Margherita"}` is the JSON document to be found
* the last parameter `$.pizzas` defines the search path: the `JSON_CONTAINS` will only check for the presence of `{"pizzaName":"Margherita"}` within the `pizzas` item in the JSON document.

Result

```
+--------+
| result |
+--------+
|      1 |
+--------+
```

The result shows that `{"pizzaName":"Margherita"}` is present. If we search for `{"pizzaName":"Hawaii"}` with:

```sql
select 
    JSON_CONTAINS(json_data,'{"pizzaName":"Hawaii"}', '$.pizzas') result from test;
```

We'll get the `0`

```
+--------+
| result |
+--------+
|      0 |
+--------+
```

## Query JSON in MySQL with JSON_CONTAINS_PATH

The above function works well if we can pinpoint the exact document and value we want to search. If, on the other side, we want to look for the existance of one or more paths in the document, we can use the `JSON_CONTAINS_PATH` function. As example, if we need to search for the presence of `shop` or `additionalToppings` we can write:

```sql
select 
    JSON_CONTAINS_PATH(json_data,'one','$.shop','$.pizzas.additionalToppings') result from test;
```

* `json_data` is the JSON column
* `one` dictates if we are looking to match only one path or all the paths (in this case we are ok to match at least one of the paths) 
* `$.shop` looks for the `shop` item within the JSON document
* `$.pizzas.additionalToppings` looks for the `additionalToppings` item within `pizzas` in the JSON document

Result

```
+--------+
| result |
+--------+
|      1 |
+--------+
```

If we try with a different set, to check if we have both (check the second parameter to `all`) the `shop` and the `drinks` items with:

```sql
select 
    JSON_CONTAINS_PATH(json_data,'all','$.shop','$.drinks') result from test;
```

We get the expected `0` since the `drinks` item is not present in the doc

```
+--------+
| result |
+--------+
|      0 |
+--------+
```

Review all the JSON MySQL use-cases listed in the [main page](/mysqljson/main)