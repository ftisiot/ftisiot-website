---
title: 'Consume Apache Kafka® messages via REST APIs'
date: "2023-08-30T11:30:24+02:00"
url: "/posts/kafka-consume-http"
description: "How to consume Apache Kafka messages via REST APIs"
tldr: "Using Karapace, an open source schema registry and REST API endpoint"
image: "/images/2023/karapace-output.png"
credit: "ftisiot"
thumbnail: "/images/2023/karapace-output.png"
categories:
- Apache Kafka
- REST APIs
- Karapace
---

You have an Apache Kafka topic that you want to consume via REST APIs (think `curl` or just a web browser), how to do it?

The reply is [Karapace](https://www.karapace.io/) an Open Source (Apache 2.0) tool providing Schema Registry and REST proxy functionality. 

Let's check out how to quickly run it on Docker.

## Step 1: the Apache Kafka® Cluster

You need an Apache Kafka cluster up & running, if you have one already you can skip to the next section. 

The [Karapace GitHub repo](https://github.com/Aiven-Open/karapace/) contains a Docker compose version of it. Alternatively you can spin a Kafka cluster up in minutes with [Aiven](https://aiven.io/) by:

* Navigating to the [Aiven Console](https://go.aiven.io/francesco-signup)
* Selecting a new Apache Kafka service
* Selecting the cloud provider and region of your preference
* Selecting the plan
* Providing the service name
* Clicking on **Create service**

After a couple minutes the Apache Kafka® cluster will be up&running. 

## Step 2: the connection parameters

In order to connect to Apache Kafka, you'll need:

* the **bootstrap URI** information
* the security information (it being `PLAINTEXT`, `SSL` or `SASL`) with the required certificates

If you are using Aiven, you can download the three certificates (`ca.pem`, `service.crt`, `service.key`) in the [Aiven Console](https://console.aiven.io/), in the Kafka service overview.

Create a folder named `certs` and place the certificates there.

## Step 3: Run Karapace REST proxy in Docker

Once you collected the required information and certificates, it's time to run the REST proxy part of Karapace on Docker with:

```bash
docker run -it  \
    -e KARAPACE_BOOTSTRAP_URI='<KAFKA_HOST>:<KAFKA_PORT>' \
    -e KARAPACE_SSL_CAFILE="/certs/ca.pem" \
    -e KARAPACE_SSL_CERTFILE="/certs/service.crt" \
    -e KARAPACE_SSL_KEYFILE="/certs/service.key" \
    -e KARAPACE_SECURITY_PROTOCOL="SSL" \
    -e KARAPACE_HOST='0.0.0.0' \
    -e KARAPACE_PORT=8082 \
    -v $(pwd)/certs:/certs \
    -p 8082:8082 \
    ghcr.io/aiven/karapace:latest ./start.sh rest
```

The command above is:
* Running the `latest` Karapace image
* Starting the `rest` part of Karapace with the `./start.sh` command
* Passing the parameters:
    * `KARAPACE_BOOTSTRAP_URI`: for the Kafka bootstrap URI, you will need to replace the placeholders
    * `KARAPACE_SECURITY_PROTOCOL`: the protocol to use to connect to Kafka, `SSL` in this example
    * `KARAPACE_SSL_*`: the list of certificates used for the `SSL` connection
    * `KARAPACE_HOST` and `KARAPACE_PORT`: the host and port used to start Karapace in the Docker container
    * `-v $(pwd)/certs:/certs`: mapping the `certs` folder created locally to `/certs` in the Docker container
    * `-p 8082:8082` mapping the port `8082` to the Docker port `8082`

If the above command is successful you should see the below message appearing

```
======== Running on http://0.0.0.0:8082 ========
```

And should be able to execute

```bash
curl http://0.0.0.0:8082 
```

Returning

```json
[]
```

## Step 3: Consume Apache Kafka® messages via REST APIs

The last step is to consume messages via REST APIs. 

We can list the topics in Kafka with:

```bash
curl "http://localhost:8082/topics"
```

In my test example the response is only a `test` topic:

```json
["test"]
```

Next we create a consumer `my_consumer` with:

```bash
Create JSON consumer
curl -X POST \
    -H "Content-Type: application/vnd.kafka.v2+json" \
    -H "Accept: application/vnd.kafka.v2+json" \
    --data '{"name": "my_consumer",  "format": "json", "auto.offset.reset": "earliest"}' \
    http://localhost:8082/consumers/json_consumers
```

Now we subscribe the `my_consumer` to `test` topic with:

```bash
curl -X POST \
    -H "Content-Type: application/vnd.kafka.v2+json" \
    --data '{"topics":["test"]}' \
    http://localhost:8082/consumers/json_consumers/instances/my_consumer/subscription
```

Finally we consume the data from the `test` topic with:

```
curl -X GET -H "Accept: application/vnd.kafka.json.v2+json" \
  http://localhost:8082/consumers/json_consumers/instances/my_consumer/records?timeout=1000
```

The output will showcase the existing data in the topic like

```json
[
  {
    "key": {
      "id": 1
    },
    "offset": 0,
    "partition": 0,
    "timestamp": 1693386610137,
    "topic": "test",
    "value": {
      "id": 1,
      "name": "francesco"
    }
  },
  {
    "key": {
      "id": 1
    },
    "offset": 1,
    "partition": 0,
    "timestamp": 1693387327332,
    "topic": "test",
    "value": {
      "id": 2
    }
  }
]
```

Karapace will sync the consumer offset to a topic in Kafka named `__consumer_offsets`, therefore the next time we issue the consume command, we'll see only the messages appeared in the Kafka topic since the last poll (the output will be empty if no new messages are in the topic).
