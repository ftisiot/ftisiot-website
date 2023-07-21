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

<!--more-->

> **NOTE**: more info is available in the [PostgreSQL JSON types documentation page](https://www.postgresql.org/docs/current/datatype-json.html)

{{< youtube O4Emf8-u-mA >}} 

<p style="border:2px dotted #77dd77;"> 👉 If you want to try it out on a FREE PostgreSQL database, check <a href="https://go.aiven.io/francesco-signup">Aiven's free plans</a></p>

## What are the differences between JSON and JSONB

### Storage
The main difference is how the data is stored: while `JSON` stores the data as text, `JSONB` optimises the information storage by creating a [tree structure](https://erthalion.info/2017/12/21/advanced-json-benchmarks/). 

### Read and Write performance

Both formats do a content validation to ensure that the input is a valid JSON document, but, while `JSON` only performs an overall check, with `JSONB` the data is then transformed from the string into the tree structure. This additional processing means than `JSON` is faster compared to `JSONB` when inserting data, due to the slimmer parsing process. On the other side, when retrieving data, specifically if only a subset of the document is selected, the `JSONB` performances are way better, since the binary tree can be parsed way faster compared to a scan of the full document and the selection of some subitems.

### Content immutability

When using `JSONB`, the content of the JSON document could be modified:

* **Unnecessary white spaces are removed**: the white spaces outside the strings, will be removed. 
  
  E.g. in `{"id":   "123"}` the 3 spaces between the `:` and the string `123` will be removed since unnecessary.

* **Duplicated keys are merged**: in case duplicated keys are present at the same level of a document, only the last value will be kept.
  
  E.g. in `{"id":"123", "name":"Ugo", "name":"Francesco"}` only the `"name":"Francesco"` association will be kept. Nested structures and arrays will be stored correctly.

* **Order of keys could be altered**: the order of the keys can be altered when inserting and retrieving an JSON document.

  E.g. the document `{"id":"123", "name":"Ugo"}` could be retrieved as `{"name":"Ugo", "id":"123"}`

Therefore if you absolutely need either to keep the white spaces, the duplicated keys or the order of the keys, you'll need to use `JSON`.

### Indexing

Since its optimized data structure, `JSONB` provides advanced methods for indexing the entire document or only some parts. Read more in the dedicated [PostgreSQL JSON indexing page](/postgresqljson/how-to-index-and-query-json-postgresql).

### Editing

With `JSON` PostgreSQL is storing the JSON document as text, therefore any edits to the document mean a full replacement of the content.
With `JSONB`, on the other hand, offers a way to just edit a particular entry in the tree, leaving the rest of the document unaltered. Read more in [How to edit a JSON document in PostgreSQL?](/postgresqljson/how-to-edit-json-postgresql).


### Summary

The following table summarizes the differences between `JSON` and `JSONB` data types in PostgreSQL.

| Quality | `JSON` | `JSONB` |
| --- | ----------- | ----------- |
| Data storage format | raw text | custom binary |
| Write | faster to write, only minimal JSON validation | slower to write due to binary conversion overhead |
| Read | slower to read, reparsing needed | faster to read |
| White spaces | preserved | removed |
| Duplicated keys | preserved | only last valued is kept |
| Order of keys | preserved | can be altered |
| Index support | no | yes |
| JSON item edit support | no | yes |



Review all the JSON PostgreSQL use-cases listed in the [main page](/postgresqljson/main)