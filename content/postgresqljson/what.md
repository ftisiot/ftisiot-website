---
title: 'What are the differences between JSON or JSONB in PostgreSQL®?'
date: "2023-01-06T16:39:26+01:00"
url: "/postgresqljson/what-are-the-differences-json-jsonb-postgresql"
description: "Understanding differences between JSON and JSONB in PostgreSQL"
tldr: "JSON stores as string with minimal validation, JSONB optimizes the content"
image: "/images/2023/pg-json-full.png"
credit: "ftisiot"
thumbnail: "/images/2023/pg-json.png"
categories:
- postgresql
- json
- jsonb
---

PostgreSQL® offers two types of data types to handle JSON data:
* `JSON` stores the JSON as text, performing a validation on the correctness of the JSON syntax
* `JSONB` optimizes the JSON storage in a custom binary format. Therefore, on top of validating the correctness of the JSON format, time is spent to properly parse and store the content.

> **NOTE**: more info is available in the [PostgreSQL JSON types documentation page](https://www.postgresql.org/docs/current/datatype-json.html)

<!--more-->

The following table defines the differences between `JSON` and `JSONB` data types in PostgreSQL.

| Quality | `JSON` | `JSONB` |
| --- | ----------- | ----------- |
| Data storage format | raw text | custom binary |
| Write | faster to write, only minimal JSON validation | slower to write due to binary conversion overhead |
| Read | slower to read, reparsing needed | faster to read |
| White spaces | preserved | removed |
| Duplicated keys | preserved | only last valued is kept |
| Order of keys | preserved | can be altered |
| Index support | no | yes |