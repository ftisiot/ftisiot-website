---
title: 'Pros and Cons of Multi Step Data Platforms'
date: "2023-05-29T15:25:59+02:00"
url: "/posts/pros-and-cons-of-multi-step-data-platforms"
description: "Discussing pros and cons about multi step data platforms"
tldr: "What are the pros and the cons about a multi step data platforms"
image: "/images/2023/newtech.png"
credit: "ftisiot"
featured_image: "/images/2023/newtech.png"
show_reading_time: true
categories:
- Data
- Platforms
- Data Pipelines
---

In the modern world, it's rare to have the data in the same shape and platform from the beginning till the end of its journey. Yes, some technologies can achieve quite a good range of functionalities but sometimes at the expense of precision, developer experience or performance. Therefore, to achieve better or faster results, people might select a new tool for a precise task and start an implementation and integration process to move the data around. 

This blog post focuses on highlighting the pros and cons of a "one shoe fits all approach" where one platform is used for all the use cases vs the "best tool for the job" where various tools and integrations are used to fulfill the requirements. 

## The initial data touchpoint

To discuss data evolution, it's necessary to define its origin. Most of the time the data is originated by some sort of application facing an internal or external audience and storing the transactions in a backend datastore. It's common for the backend to be a database (relational or not) that can immediately store and validate the data, or a streaming service (like Apache Kafka) that can forward the transaction to the relevant parties.

No matter which solution is chosen, at this point of the data journey the backend datastore must provide excellent performances in both the ability to write and read a single transaction, since the application will mainly work at single click/purchase/order level.

## Evolving with time

But, as for the intro, data rarely stays in place. On top of the transactional needs, new data stakeholders appear in the company: people in charge of the inventory are not interested in the single order movement but more in the overall daily trends; executives need to have a vision week by week of defined KPIs; selling agents want a immediate "maybe you should offer this" kinda functionality when talking to prospects. 

All the above needs are very different from the original "transactional" task and require data storage techniques, functionalities, and APIs that need to be adapted and customized for the single functionality and use-case to perform best.

Yes there are tools that can perform a wide range of functions, but is it always suggested to stick with them?

### The "Downloading to Excel" problem

I've been in the Data & Analytics space for 15 years, and for 10 of them I've been building centralized BI systems to provide a "unique source of truth" to companies. 

For the same amount of years I've been trying to battle against the "download to excel" practice: whenever a certain functionality/option/calculation wasn't available in the tool of choice, people were downloading the entire dataset and recreating the analytical process in Excel. This, on top of the security chills, was causing a plethora of different ways to calculate the same KPI, therefore defeating the "unique source of truth" mission.

How to avoid that? By having frequent feedback loops about desired functionalities or outcomes and trying to build them into the main product. But, sometimes, the centralized BI efforts were not fast, accurate, or flexible enough for the end user, therefore the leakage of data was continuing.

Later on in my career I realized that the main problem was trying to force everyone to use THE BI tool I was overlooking, and that wasn't fitting all the stakeholder needs. Therefore, it was important to include in the process that the feedback wasn't only about what to build next, but also about what other tooling and integrations were needed to best achieve the goal. In other words, one centralized team on one platform can't scale, but a centralized team empowering others to use their favorite tool via trusted integration can!

### The modern era of Business Unit as IT purchasers

Nowadays a lot of the spend is not governed by central IT. Every division in a company is able to purchase, test, deploy and manage their tool of choice to achieve their goals. When data is in the mix, this again represents the "Excel" problem, but only with nicer UIs. Therefore it's very important that data teams act as enablers allowing business units to act, improve, analyze and evolve over time by providing accurate and up to date data.

I feel that the times of "one tech fits all" are over... but what are we going to gain or miss?


## Pros of a single "one tech fits all" solution

The main pros of single "one tech fits all" solution were:

* **Unique source of truth**: the initial datastore (database) containing the data is the only source of raw transactions, no questions of "how fresh is this data" needed to be raised.
* **Transaction accuracy**: if using ACID databases, the transaction is either committed or uncommitted, therefore there is guarantee on always querying the current state.
* **Expertise**: providing several functionalities on top of the same technology means that the single tech expertise and related cost can be maximized.
* **Unique point of contact**: for security and technical queries there was a unique point of contact. The minimal amount of surface area exposed to attack.

## Cons of the "one tech fits all" solution

On the other side, the "one tech fits all" solution had also the following limitations:
* **Lack of features**: not all the features/functionalities required were included and, sometimes, if available their performance was not optimized for the workload.
* **Performance**: adding different use cases and query patterns on top of the same tool means having noisy neighbors that can affect performances.
* **Sub Optimization**: when hitting limits on the performance, the optimizations are usually a trade-off between the various use-cases requirements and not focused on achieving the best performance for a single function.
* **Baseline**: when hitting walls, like the lack of functionality, the source tech is only used as a baseline, with the majority of the work/parsing/analytics is done elsewhere.

## Pros of the "best tool for the job" solution

When choosing the best tool for the job after a proper evaluation phase, you have the following pros:

* **Tools fits exact needs**: the evaluation phase is key to understand the stakeholder requirements and buy/build the tool matching them.
* **Dedicated scaling**: scaling is purely based on the activity (or set of) that the tool needs to perform. no other use cases or noisy neighbors to optimize.
* **Performance**: the tool selection is not only based on the feature availability but also on the performances. If limits of the selected choice are hit, upgrades of the components can be tight to the particular functionality needed.
* **Dedicated Expertise**: tooling experts can be hired or acquired, focusing on exactly the functionality covered by the use case.


## Cons of the "best tool for the job" solution

On the other side, when now multiple tools are part of the data chain, you could face the follow problems:

* **Integrations**: now the data flow is the responsibility of the source platform, the target tool and the integration between them. Dedicated people need to be responsible to create and monitor the connection between tools.
* **Export performance**: data needs to be exported from the source system providing minimal disruption. The methodology and frequency of extractions needs to be agreed, possibly posing limits at the type and of integrations and freshness of data. 
* **Unique source of truth**: the same or similar datasets are now available in several tools on possibly different formats. Which should be used?
* **Security**: having data assets spread across different tools exposes companies to a wider risk of data breach or misusage.

## What lessons can we take from the "one tech fits all" to the "best tool for the job" solutions?

We somehow defined that the "best tool for the job" solution is the unavoidable choice nowadays, but has its own limits. How can we mitigate them with the lessons taken by the "one tech fits all" solution?


* **Create trusted integrations**: if data is not in a unique place, create secure, efficient, fast integrations between tools that can provide data synchronization without compromising the source platform performance. For example, tools like [Debezium](https://debezium.io/) allow an integration with some of the most used databases at the minimal cost of accessing their change logs. 
* **Transaction accuracy**: rely on source system features to address consistency of changes. Debezium [allows to track transaction metadata](https://debezium.io/documentation/reference/stable/connectors/postgresql.html#postgresql-transaction-metadata).
* **Security**: Minimize the security risks by having methods to centrally define, approve, monitor, and evaluate security settings across technology and how they affect each other. Tools like the [metadata parser](https://github.com/aiven/metadata-parser) could help in the job.


## Summary

As a data team, embrace the "best tool for the job". Your role is to provide best in class, fast, monitorable and observable integrations between systems.

Do you agree?


