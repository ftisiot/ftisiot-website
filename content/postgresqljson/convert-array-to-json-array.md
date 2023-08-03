---
title: 'How to convert an array to a JSON array in PostgreSQL'
date: "2023-01-09T17:19:50+01:00"
url: "/postgresqljson/howto-convert-array-to-json-array-postgresql"
description: ""
tldr: ""
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
- convert
- array
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use the function `array_to_json` (`array_to_jsonb` for `JSONB`) to create a JSON array from an existing array.

<!--more-->

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)

<p style="border:2px dotted #77dd77;"> 👉 Need a **FREE** PostgreSQL database? check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a></p>

## Convert an array to a JSON array with the `array_to_json` function

You can convert an array to a JSON array with the `array_to_json` (`array_to_jsonb` for JSONB) function

```
select array_to_json(
        ARRAY['🍌', '🌶️', '🍍']);
```

Result

```
  array_to_json
-----------------
 ["🍌","🌶️","🍍"]
(1 row)
```

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)