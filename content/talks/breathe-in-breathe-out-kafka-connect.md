---
title: 'Breathe in Breathe out: get Kafka Connect settings right!'
date: "2022-09-20T13:23:04+02:00"
url: "/talks/breathe-in-breathe-out-kafka-connect/"
event: "Current 2022"
location: "Austin"
site: ""
video: ""
slides: "/slides/breathe-in-breathe-out-kafka-connect/slides.pdf"
thumbnail: "/slides/breathe-in-breathe-out-kafka-connect/thumbnail.png"
image: "/slides/breathe-in-breathe-out-kafka-connect/cover.png"
description: "Breathe In, Breathe Out: get you Kafka Connect configs right"
tags:
- kafkaconnect
- apachekafka
- pitfalls
---

A deep dive into Kafka connect settings and pitfalls, analysing several tips to have a better experience when setting up a connector.

<!--more-->

![Intro image](/slides/breathe-in-breathe-out-kafka-connect/cover.png)

## Abstract

Kafka Connect is the spell book for creating magical data streaming setups. It allows us to integrate Apache Kafka with the rest of our data ecosystem and get all the data flowing to the right place. However you need some rather dark magic to configure all the weird and wonderful connectors recently, and this talk will teach you the tricks you need. 

We'll talk about streaming data into topics, the data formats to use and what to look out for when Kafka Connect is plugging data from another platform into your setup. Since we don't live in a perfect world, we'll also cover configurations like error tolerance, dead letter queues and single message transforms that can make things more robust. 

You'll see some examples of good practices, and hear some stories about how I learned a few of these things the hard way. Finally we'll shed light on some of the options, like auto evolution, that seem like a great idea when you are prototyping a new solution but which can store up problems for the longer term. 

If you are ready to make magic with Kafka Connect and the Apache Kafka ecosystem, this is the talk for you!

## Useful Links


* [Aiven for Apache Kafka®](https://aiven.io/kafka)
* [Aiven for Apache Kafka® Connect](https://docs.aiven.io/docs/products/kafka/kafka-connect.html)
* [Deep dive into Single Message Transforms](https://rmoff.net/2021/01/04/kafka-connect-deep-dive-into-single-message-transforms/)
* [JDBC Source connector: what could go wrong?](https://www.confluent.io/events/kafka-summit-london-2022/jdbc-source-connector-what-could-go-wrong/)
* [JDBC Sink connector not working with schemaless topics](https://stackoverflow.com/questions/70927512/kafka-jdbc-sink-connector-with-json-messages-without-schema)
* [Works on JDBC sink to parse schemaless topics by inferencing the JSON structure](https://cwiki.apache.org/confluence/display/KAFKA/KIP-301%3A+Schema+Inferencing+for+JsonConverter)
* [Dead Letter Queue - Twitter thread by Gunnar Morling](https://twitter.com/gunnarmorling/status/1541809606384652295)



Check out the [Aiven Rolling Challenge](go.aiven.io/aiven-challenge-current-na-22)


[💻](/slides/breathe-in-breathe-out-kafka-connect/slides.pdf)