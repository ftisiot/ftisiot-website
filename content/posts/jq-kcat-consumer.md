---
title: 'Beautify `kcat` consumer output by piping to `jq`'
date: "2022-03-31T13:39:18+02:00"
url: "/posts/jq-kcat-consumer"
description: "How to beautify kcat JSON output with jq"
tldr: "Use the `-u` flag in kcat"
image: "/images/2022/working_result_kcat_jq.jpg"
credit: ""
draft: false
thumbnail: "/images/2022/working_result_kcat_jq.jpg"
categories:
- Apache Kafka
- kcat
- jq
---

I was working on some demos recently on the Apache Kafka source connectors (hi #KafkaSummit!), and trying to display the stream of changes in the resulting Apache Kafka topic.

<!--more-->

My standard approach is to vertically divide the terminal and have on the left the database connection where I can issue commands and on the right the output of [kcat](https://docs.confluent.io/platform/current/app-development/kafkacat-usage.html). This setup works really well for simple messages, but when using it with a [Debezium source connector](https://developer.aiven.io/docs/products/kafka/kafka-connect/howto/debezium-source-connector-pg.html), the amount of fields pushed in a single message makes it hardly parsable by a live audience watching a screen.

![Unparsable JSON](/images/2022/unparsable_json.jpg)

In the above image you can notice that the `kcat` output is a JSON format without syntax highlight making it almost unreadable. 

## `jq` to the rescue

I knew and was using since long time [jq](https://stedolan.github.io/jq/) a tool that allows you to beautify and parse JSON outputs. After installing you can just use it as

```
echo '{"name":"Francesco", "preferences":["pasta","pizza","espresso"]}' | jq '.'
```

and you get your amazing JSON displayed nicely

```
{
  "name": "Francesco",
  "preferences": [
    "pasta",
    "pizza",
    "espresso"
  ]
}
```

## `jq` and `kcat`

Now, how can I apply `jq` to the `kcat` consumer output? I tried a direct pipe like:

```
kcat -F kcat.config -C \
    -t my_pgsource.public.players | jq 
```

But unfortunately I only got the output comments without the actual data being displayed

```
% Reading configuration from file kcat.config
% Reached end of topic my_pgsource.public.players [0] at offset 3
```

Seen on screen as

![No output of JSON](/images/2022/no_json_kcat.jpg)

I struggled long time, but then reached out to a friend (kudos [@rmoff](https://twitter.com/rmoff)) who pointed me to the right solution: the `-u` flag (                 Unbuffered output) in `kcat`. Once added the `-u` flag I'm now able to see my beautified JSON!

![Beautified JSON](/images/2022/working_kcat_beauty_json.gif)
