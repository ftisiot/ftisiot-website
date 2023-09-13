---
title: 'Kafka Connect sink to OpenSearch/ElasticSearch: how to sink unix timestamps'
date: "2023-09-13T10:35:51+02:00"
url: "/posts/kafka_connect_sink_elasticsearch_opensearch_timestamp"
description: "When sending unix timestamps from Kafka to OpenSearch/ElasticSearch, they are not recognized by default as timestamp, this shows how"
tldr: "Use the TimestampConverter"
image: "/images/2023/kc-es-os.png"
credit: "ftisiot"
thumbnail: "/images/2023/kc-es-os.png"
categories:
- Kafka Connect
- OpenSearch
- ElasticSearch
- timestamp
- unix time
---

When sinking unix timestamps from Apache Kafka to OpenSearch/ElasticSearch using the dedicated connector, they are not recognized by default as timestamp in the target tech. 

<!--more-->

Using the [TimestampConverter SMT](https://rmoff.net/2020/12/17/twelve-days-of-smt-day-8-timestampconverter/) you can change the format into one that is recognized natively by OpenSearch/ElasticSearch.



## Use the TimestampConverter SMT to translate unix timestamps into strings

To have OpenSearch/ElasticSearch automatically recognizing a timestamp we need to push it in the [ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) `yyyy-MM-ddTHH:mm:ss.SSSZ` where:

* `yyyy-MM-dd` are the year, month, day
* `T` is the string `T`
* `HH:mm:ss.SSS` are the hour, minutes, seconds, milliseconds
* `Z` is the string `Z`

You can achieve this by adding a TimestampConverter SMT to the Kafka connect sink definition with:

```json
"transforms.transform-name.type": "org.apache.kafka.connect.transforms.TimestampConverter$Value"
"transforms.transform-name.field": "ts_ms",
"transforms.transform-name.format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
"transforms.transform-name.target.type": "string",    
```

Where:

* `transform-name` is the transformation name, the same name must be referenced in the `transforms` parameter
* `"transforms.transform-name.type": "org.apache.kafka.connect.transforms.TimestampConverter$Value"` will extract the timestamp from part of the message value
* `"transforms.transform-name.field": "ts_ms"` identifies the field `ts_ms` as the one to be converted from unix timestamp
* `"transforms.transform-name.format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"` defines the target ISO 8601 format
* `"transforms.transform-name.target.type": "string"` defines the target field as string (OS/ES will automatically recognize it as timestamp)

