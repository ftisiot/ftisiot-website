---
title: 'No pineapple on pizza! Streaming anomaly detection with Apache Kafka® and Apache Flink®'
date: "2022-08-02T14:05:20+02:00"
url: "/talks/anomaly-detection/"
event: "Uptime 2022"
location: "Amsterdam"
site: "https://uptime.aiven.io/"
video: ""
slides: "/slides/anomaly-detection/anomaly-detection-flink-kafka.pdf"
thumbnail: "/slides/anomaly-detection/thumbnail.png"
image: "/slides/anomaly-detection/the_anomaly.png"
description: "Create an anomaly detection system with Apache Kafka® and Apache Flink®"
---

A streaming anomaly detection system with Apache Kafka® and Apache Flink®
<!--more-->

![Intro image](/slides/anomaly-detection/the_anomaly.png)

## Abstract

There's a rule in Italy that states: "pineapple doesn't belong to pizza". Yet it's a common choice around the world and a big discussion topic online.

We'll use this funny example to show the power of the best streaming open source duo: Apache Kafka and Flink. We will initially showcase how data can flow in streaming mode through Kafka topics, and then add Flink on top to detect anomalies (yep pineapple, I'm looking at you), calculate aggregations, and enrich our pipelines with data coming from external systems like a PostgreSQL database.

If you want to see the creation of a streaming data pipeline for anomaly detection in 10 minutes, this talk is for you.


## Useful Links

* [GitHub repository](https://github.com/ftisiot/anomaly-detection-with-apache-flink-and-kafka) containing all the code
* [Dockerized Fake Data Producer for Apache Kafka®](https://github.com/aiven/fake-data-producer-for-apache-kafka-docker)
* [Apache Flink® SQL documentation](https://nightlies.apache.org/flink/flink-docs-master/docs/dev/table/sql/overview/)
* [Apache Flink® MATCH_RECOGNIZE documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/dev/table/sql/queries/match_recognize/)