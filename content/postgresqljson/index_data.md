---
title: 'How to index and query a JSON object in PostgreSQL®?'
date: "2023-01-06T16:59:12+01:00"
url: "/postgresqljson/how-to-index-and-query-json-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
featured_image: "/images/2023/pg-json.png"
show_reading_time: true
categories:
- postgresql
- json
- jsonb
- index
- query
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`. This doc showcases how to index a `JSONB` column with a GIN index.

<!--more-->

Some of the examples are based on [this blog](https://scalegrid.io/blog/using-jsonb-in-postgresql-how-to-effectively-store-index-json-data-in-postgresql/).

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

{{< code "/components/pglink" >}}

## The dataset

The dataset is the following:

```
{
    "id": 778,
    "shop": "Luigis Pizza",
    "name": "Edward Olson",
    "phoneNumbers":
        ["(935)503-3765x4154","(935)12345"],
    "address": "Unit 9398 Box 2056\nDPO AP 24022",
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
* `address`: "Unit 9398 Box 2056\nDPO AP 24022"
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
create table test(id serial, json_data jsonb);

insert into test(json_data) values (
'{
    "id": 778,
    "shop": "Luigis Pizza",
    "name": "Edward Olson",
    "phoneNumbers":
        ["(935)503-3765x4154","(935)12345"],
    "address": "Unit 9398 Box 2056\nDPO AP 24022",
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

## Create a GIN index on the JSONB column

To create a GIN index over the JSONB column:

```
create index json_data_gin on test using gin (json_data);
```

## Retrieve all rows with a value

To retrieve all rows having the `shop` name `Luigis Pizza`, you can use the `@>` operator:

```
select * from test where json_data @> '{"shop":"Luigis Pizza"}';
```

Result

```
 id |                                                                                                                                                           json_data
----+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  1 | {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056\nDPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}
(1 row)
```

Checking with `EXPLAIN`:

```
explain analyse select * from test where json_data @> '{"shop":"Luigis Pizza"}';
```

Result shows the usage of the index:

```
                                                      QUERY PLAN
-----------------------------------------------------------------------------------------------------------------------
 Bitmap Heap Scan on test  (cost=41.00..42.01 rows=1 width=394) (actual time=0.398..0.399 rows=1 loops=1)
   Recheck Cond: (json_data @> '{"shop": "Luigis Pizza"}'::jsonb)
   Heap Blocks: exact=1
   ->  Bitmap Index Scan on json_data_gin  (cost=0.00..41.00 rows=1 width=0) (actual time=0.389..0.390 rows=1 loops=1)
         Index Cond: (json_data @> '{"shop": "Luigis Pizza"}'::jsonb)
 Planning Time: 0.087 ms
 Execution Time: 0.470 ms
(7 rows)
```
But, if weyou filter for a specific subitem, like:

```
explain analyse select * from test where json_data -> 'pizzas' @> '{"pizzaName":"Margherita"}';
```

You get a sequence scan:

```
                                            QUERY PLAN
---------------------------------------------------------------------------------------------------
 Seq Scan on test  (cost=0.00..470.13 rows=69 width=394) (actual time=2.064..2.064 rows=0 loops=1)
   Filter: ((json_data -> 'pizzas'::text) @> '{"pizzaName": "Margherita"}'::jsonb)
   Rows Removed by Filter: 6939
 Planning Time: 0.056 ms
 Execution Time: 2.085 ms
(5 rows)
```

If the above filter is common, you might want to create specific indexes for a single item (`pizzas` in this case).

## Create an index on a specific JSONB item

To create an index for a specific item like the `pizzas` you can:

```
CREATE INDEX pizzas_gin ON test USING GIN ((json_data -> 'pizzas'));
```

## Retrieve all rows with a specific filter

To filter for a specific item value, like `pizzas` having `pizzaName` equal to `Margherita` you can use the `@>` operator:

```
explain analyse select * from test where json_data -> 'pizzas' @> '{"pizzaName":"Margherita"}';
```

Results

```
 id |                                                                                                                                                           json_data
----+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  1 | {"id": 778, "name": "Edward Olson", "shop": "Luigis Pizza", "image": null, "pizzas": [{"pizzaName": "Salami", "additionalToppings": ["🥓", "🌶️"]}, {"pizzaName": "Margherita", "additionalToppings": ["🍌", "🌶️", "🍍"]}], "address": "Unit 9398 Box 2056\nDPO AP 24022", "phoneNumbers": ["(935)503-3765x4154", "(935)12345"]}
(1 row)
```

Checking with `EXPLAIN`:

```
explain analyse select * from test where json_data @> '{"pizzas": [{"pizzaName":"Margherita"}]}';
```

Results, the index is used:

```
                                                      QUERY PLAN
-----------------------------------------------------------------------------------------------------------------------
 Bitmap Heap Scan on test  (cost=43.00..44.01 rows=1 width=394) (actual time=0.493..0.494 rows=1 loops=1)
   Recheck Cond: (json_data @> '{"pizzas": [{"pizzaName": "Margherita"}]}'::jsonb)
   Heap Blocks: exact=1
   ->  Bitmap Index Scan on json_data_gin  (cost=0.00..43.00 rows=1 width=0) (actual time=0.483..0.483 rows=1 loops=1)
         Index Cond: (json_data @> '{"pizzas": [{"pizzaName": "Margherita"}]}'::jsonb)
 Planning Time: 0.186 ms
 Execution Time: 0.523 ms
(7 rows)
```
