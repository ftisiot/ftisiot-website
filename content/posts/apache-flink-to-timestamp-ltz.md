---
title: 'Convert Epoch to Timestamps in Apache Flink with ``TO_TIMESTAMP_LTZ``'
date: "2022-08-29T11:19:57+02:00"
url: "/posts/apache-flink-to-timestamp-ltz"
draft: false
description: 'How to Convert Epoch to Timestamps in Apache Flink with ``TO_TIMESTAMP_LTZ``'
tldr: "Use ``TO_TIMESTAMP_LTZ`` with the appropriate parameter"
image: "/images/2022/flink-timestamps.png"
credit: ""
featured_image: "/images/2022/flink-timestamps.png"
show_reading_time: true
categories:
- ApacheFlink
- Timestamps
---

Sometimes when defining an Apache Flink® table using SQL we need to map an [epoch timestamp](https://en.wikipedia.org/wiki/Unix_time) and use it as record/message timestamp, this blog contains few tricks to get it right. 

<!--more-->

Doing it properly is not complex, but some attention needs to be paid since small changes can have huge impact (ask me how I know... lost a good hour on it today).

## The ``TO_TIMESTAMP_LTZ`` function

When dealing with epoch timestamps, Flink offers the ``TO_TIMESTAMP_LTZ`` function, with a good set of [documentation](https://nightlies.apache.org/flink/flink-docs-master/docs/dev/table/timezone/) around it. Basically the function can be used with epoch timestamps in either seconds or milliseconds.

* For epoch timestamp in **seconds** use: ``TO_TIMESTAMP_LTZ(timestamp_column, 0)``
* For epoch timestamp in **milliseconds** use: ``TO_TIMESTAMP_LTZ(timestamp_column, 3)``

## What about the ``timestamp_column`` definition?

Ok, ok, that works! What about the definition of the ``timestamp_column``? You need to pay attention to it as well!

If the epoch timestamps in **seconds** you can get away with it by declaring the column as `INT`.
But if you do the same when dealing with epoch timestamps in **milliseconds** you'll face an error like 

```
Caused by: java.lang.NumberFormatException: For input string: "1661760207278"
```

This is because the number is greater than the `INT` range (`-2,147,483,648` to `2,147,483,647`). Therefore is better to have the column declared as `BIGINT`, like the following 

```
ts BIGINT,
name string,
ts_ltz AS TO_TIMESTAMP_LTZ(ts, 3),
WATERMARK FOR ts_ltz AS ts_ltz - INTERVAL '10' SECOND
```


