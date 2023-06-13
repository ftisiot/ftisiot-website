---
title: 'How to create a JSON object from keys and values arrays in PostgreSQL®?'
date: "2023-01-09T17:25:47+01:00"
url: "/postgresqljson/howto-create-json-from-keys-and-values-arrays-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- create
- array
- key
- value
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use the function `json_object` (`jsonb_object` for `JSONB`) to create a JSON object from two arrays of keys and values.

<!--more-->

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

> ℹ️ If you want to try it out on a FREE PostgreSQL database, check [Aiven's free plans](https://console.aiven.io/signup)

## Create a JSON object from two arrays of keys and values with the `json_object` function

You can create a JSON object from two arrays (one of keys and one of values) with the `json_object` (`jsonb_object` for JSONB) function

```
select 
    jsonb_object(
        ARRAY['name', 'pizza'], 
        ARRAY['Francesco', 'Margherita']);
```

Result

```
                 jsonb_object
----------------------------------------------
 {"name": "Francesco", "pizza": "Margherita"}
(1 row)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)
