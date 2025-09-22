---
title: 'How to build a JSON array from a list of elements in PostgreSQL®'
date: "2023-01-09T17:14:22+01:00"
url: "/postgresqljson/how-to-build-json-array-from-elements-postgresql"
description: "Learn how to create JSON arrays from elements in PostgreSQL using json_build_array and jsonb_build_array functions. Complete tutorial with examples and best practices."
tldr: "Create JSON arrays from elements in PostgreSQL using json_build_array function with practical examples"
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
featured_image: "/images/2023/pg-json.png"
show_reading_time: true
categories:
- postgresql
- json
- jsonb
- elements
- array
tags:
- postgresql
- json
- jsonb
- array
- database
- tutorial
- how-to
author: "Francesco Tisiot"
lastmod: "2023-01-09T17:14:22+01:00"
draft: false
sitemap:
  changefreq: monthly
  priority: 0.7
---

PostgreSQL® offers two types of data types to handle JSON data, `JSON` and `JSONB`, you can use the function `json_build_array` (`jsonb_build_array` for `JSONB`) to create a JSON array from a list of elements.


<!--more-->

> **NOTE**: To review the differences between `JSON` and `JSONB` check out the [related article](/postgresqljson/what-are-the-differences-json-jsonb-postgresql).

> **NOTE**: more info is available in the [PostgreSQL JSON functions documentation page](https://www.postgresql.org/docs/current/functions-json.html)




## Create a JSON array from a list of elements with the `json_build_array` function

You can create a JSON array from a list of elements with the `json_build_array` (`jsonb_build_array` for JSONB) function

```
select 
    jsonb_build_array(1,4,'🍌', 'pizza');
```

Result

```
  jsonb_build_array
-----------------------
 [1, 4, "🍌", "pizza"]
(1 row)
```

> **NOTE**: the array elements can be heterogeneously-typed

Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main/)

