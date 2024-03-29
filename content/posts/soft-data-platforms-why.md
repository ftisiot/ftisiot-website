---
title: 'From data stack to data stuck: the risks of not asking the right data questions'
author: ftisiot
date: "2022-12-21T11:35:42+01:00"
url: "/posts/from-data-stack-to-data-stuck"
description: "What are the risks of not evaluating a data platform correctly?"
tldr: "When designing new data platforms, failing to perform a complete assessment of the options available can have disastrous effects on a company's capability to scale and make data secure, discoverable, usable and reusable."
image: "/images/2022/stuck.png"
credit: ""
featured_image: "/images/2022/stuck-t.png"
show_reading_time: true
categories:
- Design
- Data Platforms
- Evaluation
---

 
Companies are in a continuous motion: new requirements, new data streams, new technologies are popping up every day. When designing new data platforms supporting the needs of your company, failing to perform a complete assessment of the options available can have disastrous effects on a company's capability to innovate, and making sure their data assets usable and reusable in the long term.

<!--more-->

Having a standard assessment methodology is an absolute must to avoid personal bias, and properly evaluate the various solutions across all the needed axes. The [SOFT Methodology](https://aiven.io/blog/a-soft-methodology-to-define-robust-data-platforms) provides a comprehensive guide of all the evaluation points to define robust and future proof data solutions. However, the original blog doesn’t discuss a couple of important factors: **why** is applying a methodology like SOFT important? and, even more, what **risks** can we encounter if we’re not doing so? This blog aims to cover both aspects.
 
# The why
 
Data platforms are here to stay: the recent history of technology has told us that data decisions made now have a long lasting effect.
 
We commonly see a frequent rework of the front-end, but radical changes in the back-end data platforms used are rare. Front-end rework can radically change the perception of a product, but when the same is done on a backend the changes are not immediately impacting the end users.
 
Changing the product provider is nowadays quite frictionless, but porting a solution across different backend tech stacks is, despite the eternal promise, very complex and costly, both financially and time wise. Some options exist to ease the experience, but the code compatibility and performances are never a 100% match.
 
Furthermore, when talking about data solutions, performance consistency is key. Any change in the backend technology is therefore seen as a high risk scenario, and most of the times refused with the statement "don’t fix what isn’t broken". The fear of change blocks both new tech adoption as well as upgrades of existing solutions.
 
Summarizing, the world has plenty of examples of companies using backend data platforms chosen ages ago, sometimes with old, unsupported versions. Therefore, any data decision made today needs to be robust and age well, in order to support the companies in their future data growth. Having a standard methodology helps understand the playing field, evaluate all the possible directions and accurately compare the options.
 
# The risks of being (data) stuck
 
Ok, you're in the long term game now. Swapping back-end or data pipeline solutions is not easy, therefore selecting the right one is crucial. But what problems will we face if we fail in our selection process? What are the risks of being stuck with a sub-optimal choice?
 
## Features

![List of Features](/images/2022/features-list.png)

When thinking about being stuck, it's tempting to compare the chosen solution with the new and shiny tooling available at the moment, and their promised future features.
 
New options and functionalities could enhance a company’s productivity, system management, integration, and remove frictions in any point of the data journey. Being stuck with a suboptimal solution without a clear innovation path and without any capability to influence its direction puts the company in a potential weak position regarding innovation. Evaluating the community and the vendors behind a certain technology could help decrease the risk of stagnating tools.
 
It's very important to evaluate which features and functionality is relevant/needed and define a list of "must haves" to reduce time spent on due diligence.
 
## Scaling

![Scaling](/images/2022/growth.png)
 
The [SOFT methodology blog post](https://aiven.io/blog/a-soft-methodology-to-define-robust-data-platforms) touches several directions on scaling: human, technological, business case, financial.
 
Hitting any of these problems could mean that the identified solution:
* could not be supported by lack of talent
* could hit technical limits and prevent growth
* could expose security/regulatory problems
* could be perfectly fine to run on a sandbox, but financially impractical on production-size data volumes
 
Hitting scaling limits therefore means that companies adopting a specific technology could be forced to either slow down growth or completely rebuild solutions starting from a different technology choice.
 
## Support and upgrade path

![Support](/images/2022/hands.png)
 
Sometimes the chosen technology advances, but companies are afraid or can't find the time/budget to upgrade to the new version. The associated risk is that the older the software version, the more complex (and risky) the upgrade path will be. In exceptional circumstances, the upgrade path could not exist, forcing a complete re-implementation of the solution.
 
Support needs a similar discussion: staying on a very old version could mean a premium support fee in the best case, or a complete lack of vendor/community help in a vast majority of the scenarios.
 
## Community and talent

![Community and talent](/images/2022/people.png)
 
The risk associated with talent shortage was already covered in the scaling chapter. New development and workload scaling heavily depend on the humans behind the tool. Moreover, not evaluating the community and talent pool behind a certain technology decision could create support problems once the chosen solution becomes mature and the first set of developers/supporters leave the company without proper replacement.
 
The lack of a vibrant community around a data solution could rapidly decrease the talent pool, creating issues for new features, new developments and existing support.
 
## Performance

![Performance](/images/2022/clock.png)

It's impossible to know what the future will hold in terms of new technologies and integrations. But selecting a closed solution, with limited (or none) capabilities of integration forces companies to run only "at the speed of the chosen technology" exposing companies to a risk of not being able to unleash new use cases because of technical limitations.
Moreover, not paying attention to the speed of development and recovery could expose limits on the innovation and the resilience fronts.
 
## Black box

![Black box](/images/2022/blackbox.png)

When defining new data solutions, an important aspect is the ability to make data assets and related pipelines discoverable and understandable. Dealing with a black box approach means exposing companies to repeated efforts and inconsistent results which decrease the trust in the solution and open the door to misalignments on the results across departments.
 
 
## Overthinking

![Overthinking](/images/2022/questions.png)
 
The opposite risk is overthinking: the more time spent evaluating solutions, the more technologies, options and needs will pile up making the final decision process even longer.
An inventory of the needs, timeframes and acceptable performance are necessary to reduce the scope, take a decision and start implementing.
 
 
# Conclusion
 
When designing a data platform, it is very important to address the right questions and avoid the "risk of being stuck". The [SOFT Methodology](https://aiven.io/blog/a-soft-methodology-to-define-robust-data-platforms) aims at providing all the important questions you should ask yourself in order to avoid pitfalls and create a robust solution.
 
Do you feel all the risks are covered? Have a different opinion? Let me know!