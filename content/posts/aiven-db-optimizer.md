---
title: "Helping PostgreSQL professionals with AI-assisted performance recommendations"
date: 2024-05-28T15:48:13+02:00
categories:
- PostgreSQL
- AI
- performance
tags: [PostgreSQL, AI, performance]
featured_image: "/images/2024/dboptimizer.png"
description: "Helping PostgreSQL professionals with AI-assisted performance recommendations"
show_reading_time: true
---

Since the beginning of my journey into the data world I've been keen on making professionals better at their data job. In the previous years that took the shape of creating materials in several different forms that could help people understand, use, and avoid mistakes on their data tool of choice. But now there's much more into it: a trusted AI solution to help data professional in their day to day job.

<!--more-->

{{< code "/components/pglink" >}}

## From content to tooling

The big advantage of the content creation approach is the 1-N effect: the material, once created and updated, can serve a multitude of people interested in the same technology or facing the same problem. You write an article once, and it gets found and adopted by a vast amount of professionals.

The limit of content tho, it's that it is an extra resource, that people need to find and read elsewhere. While this is useful, it forces a context switch with moving people away from the problem they are facing. Here is where tooling helps, providing assistance in the same IDE that professionals are using for their day to day work.

## Tooling for database professionals

I have the luxury of working for [Aiven](https://aiven.io/) which provides professionals an integrated platform for all their data needs. In the last three years I witnessed the growth of the platform and its evolution with the clear objective to make it better usable at scale. Tooling like [integrations](https://aiven.io/integrations-and-connectors), [Terraform providers](https://aiven.io/docs/tools/terraform) and the [Console](https://go.aiven.io/francesco-signup) facilitate the work that platform administrators have to perform on daily basis.

But what about the day to day work of developers and CloudOps teams? This was facilitated when dealing with administrative tasks like backups, creation of read only replicas or upgrades, but the day to day work of optimizing the workloads was still completely on their hands.

## Using trusted AI to optimize database workloads

This, however, is now changing. With the recent launch of [Aiven AI Database Optimizer](https://go.aiven.io/ft-ai-db-optimizer) we are able to help both developer and CloudOps in their day to day optimization work!

Aiven AI Database Optimizer provides, directly in the [Console](https://go.aiven.io/francesco-signup), insights on database workloads alongside a one-click Optimize button that suggests index and SQL rewrites. It's a non intrusive solution who gathers informations from the slow query log and database metadata, and leverages an in-built AI engine, to provide accurate suggestions to improve performance.

{{< youtube fOavII9QAmg >}}

The solution, based on the [EverSQL by Aiven](https://www.eversql.com/) technology has been already adopted by 120.000 professionals optimizing over 2 million queries. It's not a wrapper around a public Generative AI provider, is a dedicated solution that keeps data privacy and security as a priority.

You can experience it for Free in the Early Availability phase, just navigate to the [Aiven Console](https://go.aiven.io/francesco-signup) and create an Aiven for PostgreSQL® service! Once you have some workload on the service, the **AI Insights** page will show you the workloads and provide index and SQL rewrite suggestion to take your database performance to the next level!
