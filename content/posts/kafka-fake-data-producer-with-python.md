---
author: ftisiot
date: 2021-01-19 15:30:00+00:00
draft: true
title: A Pizza Fake Kafka streaming dataset with Python
type: post
url: /2021/01/15/fake_data_python/
image: "/images/2021/01/looking_forward.jpg"
thumbnail: "/images/aiven_small.jpeg"
credit: "https://twitter.com/ftisiot/"
categories:
- work
tags:
- work
- aiven
- cli
- python
- commandline
---


We're all been there: we installed or purchased a new and shiny data management tool in order to test it, but  immediately realised we don't have any data to use for our trial! Maybe we know the data's schema but we can't use our company's datasets for a number of reasons.

How can we give the platform a proper try? What if I tell you all you need are a few lines of Python code?
We'll use a real case of a pizza delivery chain to demonstrate how easy it is to produce proper fake data!


<---more--->

# Data platforms are empty skeletons

Databases, Data Lakes, and more in general Datastores are cool pieces of technology allowing everybody to manage and analyse data but they share a common problem: they are completely empty by default!
Several platforms solve this problem by offering pre-filled sample datasets that can be installed or enabled with the help of few commands. Those datasets, however, are pretty static and not always provide the set of features, fields or cardinality people need to perform their trials.

With Apache Kafka the size of this problem is even bigger: not only it's empty by default, but it is also a streaming data platform that works by ingesting, transforming and distributing data on the fly expecting a continuous flow of data! Finding streaming data sources is a hard task, and, especially if you just want to test the basics of the platform, setting them up properly can be quite cumbersome.

Creating fake data by hand is also not trivial, even if you know your data schema, creating a coherent set of rows from scratch is challenging. In this blog post we'll see how to create such a fake dataset for Kafka with a hot topic in mind: Pizzas!


# Kafka setup

Let's start with the tech setup! For this example we'll need a kafka cluster. Creating it on Aiven.io is really easy, all we need to do is
* Sign up in [Aiven.io console](https://console.aiven.io)
* Click on **+ Create a new service**
* Select the **Kafka** service (if we're picky we can also choose our favourite Apache Kafka version)

![kafka versions](/images/2021/01/kafka_fake_pizza/kafka_service_versions.png)
* Select the Cloud provider we want to deploy our services to, together with the Cloud region
![providers](/images/2021/01/kafka_fake_pizza/providers.png)
* Select the service plan based on our needs
![service_plan](/images/2021/01/kafka_fake_pizza/service_plan.png)
* Give the service a name
![service_name](/images/2021/01/kafka_fake_pizza/service_name.png)

Since I'm based in Italy I could easily go for the recently created `aws-eu-south` AWS region located in Milan to minimise latency. Your choice will depend on where you're located or where you plan to provide your services. For my initial test I'm ok in using a **Startup plan** knowing I can always upgrade (or downgrade) in the future.

Once clicked on **Create Service**, we just need a few minutes before my Kafka 3-node cluster becomes in `RUNNING` state!
But we don't need to wait, we can already start preparing for the next step: downloading the certificates required to connect!
![credentials](/images/2021/01/kafka_fake_pizza/credentials.png)
We can easily access the **Access Key**, **Access Certificate** and **CA Certificate** from the **Overview** tab of my Kafka instance in [Aiven.io console](https://console.aiven.io). Downloading them in my computer folder will create 3 files:
* `service.key`: the Access Key
* `service.cert`: the Access Certificate
* `ca.pem`: the CA Certificate

In the **Overview** tab I can also take note of the **Service URI** (usually in the form `<INSTANCE_NAME>-<PROJECT_NAME>.aivencloud.come:<PORT>`) that we'll use to correctly point our producer to the Kafka cluster.

## Topic Creation

While we're in the [Aiven.io console](https://console.aiven.io) we can go to the **Topics** section and create a Topic named `pizza-orders` where we'll push our data. We can review the advanced parameters like **Replication** factor or number of **Partitions** and click on **Add Topic**
![topic_name](/images/2021/01/kafka_fake_pizza/new_topic.png)

The topic creation in [Aiven.io console](https://console.aiven.io) (or via [Aiven's cli](https://github.com/aiven/aiven-client#aiven-client-) ) is required because by default producers can push data only to pre-created topics. If you wanna enable producers to create a topics on-the-fly while pushing the first record, you can do so by enabling the `kafka.auto_create_topics_enable` parameter in [Aiven.io console](https://console.aiven.io) **Overview** tab, scrolling down to the **Advanced configuration** section
![advanced_configurations](/images/2021/01/kafka_fake_pizza/advanced_configurations.png)
Tip: the `kafka.auto_create_topics_enable` parameter is available under the **+ Add configuration option** menu.

## Enabling Kafka REST APIs
The last step needed in the configuration is to enable **Kafka REST API (Karapace)** for our cluster. This step is, strictly speaking, not fundamental. But will allow us to check our producer by [reviewing the pushed records in Aiven.io console](https://aiven.io/blog/kafka-messages-ui-announcement) **Topics** tab.
![enable Kafka rest api](/images/2021/01/kafka_fake_pizza/enable_kafka_rest.png)

# Python client setting
We'll use the [kafka-python](https://pypi.org/project/kafka-python/) client to build our producer. All we need to do is installing it with
```
pip install kafka-python
```
And configure it properly
```
import json
from kafka import KafkaProducer

certificatesFolderName = "~/kafkaCerts/kafka-pizza/"
producer = KafkaProducer(
    bootstrap_servers="<INSTANCE_NAME>-<PROJECT_NAME>.aivencloud.come:<PORT>",
    security_protocol="SSL",
    ssl_cafile=folderName+"ca.pem",
    ssl_certfile=folderName+"service.cert",
    ssl_keyfile=folderName+"service.key",
    value_serializer=lambda v: json.dumps(v).encode('ascii'),
)
```
Where `bootstrap_servers`, `ssl_cafile`, `ssl_certfile` and `ssl_keyfile` are referring to the connection URI and the three certificate files mentioned in the section above.

The `value_serializer` and `key_serializer` parameters need a separate explanation: we will later produce each record and key in Json format, to push it properly to Kafka we need to transform them to string and encode. This is exactly what the code  `lambda v: json.dumps(v).encode('ascii')` does.

# Create Fake Datasets with Faker
So...back to the topic...Pizza!

We are the owners of a pizza delivery chain, and of course we want to push our orders to Apache Kafka! We know we receive calls, and note down the client's `Name`, `Address` and `Phone Number` (you never know we get lost while delivering). How can we mimic that information?

Welcome to [Faker](https://faker.readthedocs.io/en/master/)!
A Python library allowing us to create proper fake data!

After installing it

```
pip install Faker
```
we just need a simple code to create one (or more) tuple of data containing the `Name`, `Address` and `Phone Number`

```
from faker import Faker
fake = Faker()
message= {
        'name':fake.name(),
        'address':fake.address(),
        'phone':fake.phone_number()
        }
print(message)
```

which will print a record like the below

```
{'name': 'Adrian Cole', 'address': '9395 Smith Canyon\nSullivanport, UT 22200', 'phone': '001-959-108-3651'}
```
We can even localise the output by passing the locale as argument like
```
fake = Faker('it_IT')
```
The same example above, localised in Italian with the `it_IT` parameter will generate

```
{'name': 'Sig. Leopoldo Piacentini', 'address': 'Piazza Cocci 707 Piano 3\nSesto Isabella lido, 53704 Forlì-Cesena (FE)', 'phone': '+39 12 26548428'}
```

Perfect, we have the basic fake data generator ready!

Well... a `<Name, Address, Phone Number>` tuple is not really rocket science, and also doesn't tell us anything about our business! We are a pizzeria, where are the pizzas?
Surprisingly [Faker's standard providers](https://faker.readthedocs.io/en/master/providers.html) does not include any pizza generator, but we can [create our own](https://faker.readthedocs.io/en/master/providers.html)!

## Create Custom Data Provider
We know we have a standard pizza menu consisting of few options going from the traditional **Margherita** to the **Mari & Monti** mixing seafood and ham (and no, we don't offer **Hawaii** pizza). Creating a fake pizza generator is just a matter of returning a random choice between the available options

```
from faker.providers import BaseProvider

class PizzaProvider(BaseProvider):
    def pizzaName(self):
        validPizzaNames= ['Margherita',
                          'Marinara',
                          'Diavola',
                          'Mari & Monti',
                          'Salami',
                          'Pepperoni'
                        ]
        return validPizzaNames[random.randint(0,len(validPizzaNames)-1)]
```
If we add the provider and run it for 10 samples
```
fake.add_provider(PizzaProvider)
for i in range(0,10):
    print(fake.pizzaName())
```
We correctly obtain
```
Mari & Monti
Salami
Marinara
Pepperoni
Marinara
Pepperoni
Salami
Pepperoni
Margherita
Pepperoni
```

But like in any respectable pizzeria, we allow people to add toppings from a list, and similarly to the above, we can define a custom `pizzaToppings` function within the `PizzaProvider` as

```
def pizzaTopping(self):
    availablePizzaToppings= ['tomato',
                         'mozzarella',
                         'blue cheese',
                         'salami',
                         'green peppers',
                         'ham',
                         'olives',
                         'anchovies',
                         'artichokes',
                         'olives',
                         'garlic',
                         'tuna',
                         'onion',
                         'pineapple',
                         'strawberry',
                         'banana'
                         ]
    return availablePizzaToppings[random.randint(0,len(availablePizzaToppings)-1)]
```
As you can see, we're pretty open to customers choosing their own toppings but we'll probably refuse to serve clients ordering one of the last 3 choices.

Since we're the owners of a chain, We still miss one field: the **pizza shop** receiving the order! As per above, a simple custom provider will make the trick

```
def pizza_shop(self):
    pizza_shops = [
        'Marios Pizza',
        'Luigis Pizza',
        'Circular Pi Pizzeria',
        'Ill Make You a Pizza You Can''t Refuse',
        'Mammamia Pizza',
        'Its-a me! Mario Pizza!'
        ]
    return pizza_shops[random.randint(0, len(pizza_shops)-1)]
```

## Creating an order

We now have all the building blocks, let's create an order!
For each call, as per above, we note down the `Name`, `Address` and `Phone Number`. The customer however can order an arbitrary number of pizzas (that we'll limit to 10), and, within each pizza, an arbitrary number of additional toppings (that we'll limit to 5)

```
# Each Order can have 1-10 pizzas in it
key = fake.pizza_shop()
pizzas = []
for pizza in range(random.randint(1, 10)):
    # Each Pizza can have 0-5 additional toppings on it
    toppings = []
    for topping in range(random.randint(0, 5)):
        toppings.append(fake.pizza_topping())

    pizzas.append({
        'pizzaName': fake.pizza_name(),
        'additionalToppings': toppings
        })
    message = {
        'id': i,
        'shop': key,
        'name': fake.unique.name(),
        'phoneNumber': fake.unique.phone_number(),
        'address': fake.address(),
        'pizzas': pizzas
        }

```
The above code will generate an output like

```
{
  "id": 704,
  "shop": "Luigis Pizza",
  "name": "Jessica Green",
  "phoneNumber": "(549)966-3806x9591",
  "address": "458 Conway Dale Apt. 510\nZacharyborough, TX 48185",
  "pizzas": [
    {
      "pizzaName": "Mari & Monti",
      "additionalToppings": [
        "banana"
      ]
    },
    {
      "pizzaName": "Peperoni",
      "additionalToppings": [
        "ham"
      ]
    },
    {
      "pizzaName": "Mari & Monti",
      "additionalToppings": [
        "garlic",
        "anchovies",
        "banana"
      ]
    }
  ]
}

```


# Creating a Fake Producer
We described above the two lego blocks: Kafka producer settings and fake order generator, what's missing?
We need a continuous stream of events, we can easily simulate it with a loop

```
while i <  nrMessages:
    # Setting the Key as the shop name
    key = fake.pizza_shop()
    # Each Order can have 1-10 pizzas in it
        pizzas = []

    ...

    message={
            ...
            }

    print("Sending: {}".format(message))
    # Sending the message to Kafka
    producer.send("pizza-orders",
                  key={'shop': key},
                  value=message)
    # Some sleep time before the next message
    time.sleep(sleepTime)

    # Force sending of all messages
    if (i % 100) == 0:
        producer.flush()
    i=i+1
producer.flush()
```

One thing to notice: with `key=fake.pizza_shop()` we are keying our message with the shop name, this will ensure all events from the same shop to appear in the same `pizza-order` topic partition, thus making sure that a shop's requests will be executed following the proper order.

The full code example is visible [here](link_to_code). After executing it, we can verify in [Aiven.io console](https://console.aiven.io) **Topics** tab, that the Producer did its job by checking the `pizza-orders` offset

![offset](/images/2021/01/kafka_fake_pizza/topic_info.png)

And, since we enabled Kafka REST APIs, also by viewing the actual topic content

![topic content](/images/2021/01/kafka_fake_pizza/topic_message_view.png)

Last but not least, we can see the producer/consumer pipeline in action, by reading the `pizza-orders` topic with [kafkacat](https://help.aiven.io/en/articles/2607674-using-kafkacat)!

![producer consumer](/images/2021/01/kafka_fake_pizza/producer-consumer.gif)

We are adding more and more capabilities to Kafka every day, check them out and start your trial now! Bla bla bla call to action
