---
author: ftisiot
date: 2019-12-13 13:09:17+00:00
draft: false
title: 2019 a Year of Conferences and Much More!
type: post
url: /2019/12/13/2019-a-year-of-conferences-and-much-more/
tags:
- conferences
- data science
- datascience
- Kafka
- machine learning
- Oracle
- Oracle Analytics Cloud
- Oracle Machine Learning
classes:
- feature-tweetquote
---

The 2019 end-of-year holiday period is approaching, before closing the laptop for few days it's good to look back how the year went. It has been very busy working wise: client work, conference preparation and talks, blog posts and videos kept my time well occupied. I also had an amazing news in June, when, during [Kscope 19](https://kscope19.odtug.com), I was invited on the stage and announced as [Oracle ACE Director](https://www.oracle.com/technetwork/community/oracle-ace/index.html)

{{< tweet 1143242212114722816 >}}

The ACE community is an amazing group of people willing to donate part of their time for the community, and I feel lucky being part of it!

Now, let's see with a bit of detail what happened in the year we're about to close!


## January - ITOUG Tech Days 2019


January for me is a very busy month, I'm part of the fantastic [Italian Oracle User Group Board](https://itoug.it/organizzazione/) preparing the yearly conference named Tech Days. Since few years the event has a double location Milan and Rome. I presented, joined by Christian Berg about "[Starting an Oracle Analytics Journey from 0](https://itoug.it/wp-content/uploads/2019/02/Francesco-Tisiot-OAC-From-A-to-Z.pdf)" covering the basics of what OAC is capable of.

https://twitter.com/ElffarAnalytics/status/1091282096679800832?ref_src=twsrc%5Etfw

The ITOUG community is growing, with [Tech Days 2020](https://itoug.it/eventi/tech-days-2020/) coming next January, last year the event was a success and I believe we made both attendees and speakers happy. For the latter we organised a guided tour in Rome including a visit of the Colosseum!

https://twitter.com/IT_OUG/status/1090970197547200512?ref_src=twsrc%5Etfw

Last but not least, it's always nice to keep the traditions live, like Mr Berg not being able to fly back straight after the Rome event (2nd year in a row), this time joined by Mr Gianni Ceresa (look for #TheBergEffect for more details).

https://twitter.com/FTisiot/status/1091384372660449281?ref_src=twsrc%5Etfw

Read more about ITOUG Tech Days 2019 in [Heli's article in OraWorld](http://www.oraworld.org/fileadmin/documents/14-ORAWORLD.pdf)!


## March - Analytics & Data Summit and OUG Norway


March has been a busy month conference wise, with both the [Analytics & Data Summit](https://analyticsanddatasummit.org/schedule/) and the [Oracle User Group Norway](https://ougn2020.com) happening almost side by side! I had several talks in both of them mainly related to the figure of the Data Scientist and how [Oracle Analytics Cloud allows to start your path into data science and machine learning](https://speakerdeck.com/ftisiot/become-a-data-scientist). One of the most popular talks merged two of my favourite topics: Wine and data science! the "[Is it Corked : Wine Machine Learning Predictions with OAC](https://speakerdeck.com/ftisiot/is-it-corked-wine-machine-learning-predictions-with-oac-474fe035-e821-4531-9cf4-62e3224cd622)" was presented in several conferences over the year!

https://twitter.com/FTisiot/status/1102906415151222785?ref_src=twsrc%5Etfw

The talks were based on a series of blog posts about [Democratising Data Science with OAC](https://www.rittmanmead.com/blog/2019/04/democratize-data-science-with-oracle-analytics-cloud/).

Both conferences have been really interesting: the A&D Summit includes a lot of very interesting content if you are in the Analytics world, and the Norway boat... well is the boat! Great content and spectacular location!

Recent news: I'll be back at [Analytics and Data Summit 2020](https://analyticsanddatasummit.org) with several talks! Hope to see you there!


## April -  Game Of Thrones


In April the last Game of Thrones series was being released together with my usual [sentiment analysis with Confluent's Kafka and Google Cloud Natural Language APIs and Python](https://www.rittmanmead.com/blog/2019/04/game-of-thrones-8-real-time-sentiment-scoring/)

![](https://www.rittmanmead.com/blog/content/images/2019/04/image-38.png)


Using Python to write a Consumer/Producer that reads from Kafka, calls the Google Cloud Natural Language APIs and then pushes the sentiment score back in Kafka was a nice test.


## May - EMEA IaaS and PaaS Forum in Rome


The Iaas and PaaS forum event hosted in Rome aimed at strengthening the relationship between Oracle and their partners. During the event I presented a quick 15 min talk about "[Data Science Best Practices with OAC](https://speakerdeck.com/ftisiot/data-science-with-oac-best-practises)".

https://twitter.com/FTisiot/status/1125808611387674624?ref_src=twsrc%5Etfw


## June - Kscope


As mentioned above June was for me a big hit! I was nominated ACE Director during the opening Keynote of Kscope19! Kscope is an amazing conference with huge takeaways in terms of content and people, I'm sad next year I won't be able to attend for personal impediments! At Kscope19 I had two talks: the "[Is it Corked: Wine ML predictions with OAC](https://speakerdeck.com/ftisiot/is-it-corked-wine-machine-learning-predictions-with-oac-474fe035-e821-4531-9cf4-62e3224cd622)" and a new one "[Become an Equilibrista: find the right balance in the analytics Tech Ecosystem](https://speakerdeck.com/ftisiot/become-an-equilibrista-find-the-right-balance-in-the-analytics-tech-ecosystem-dad5788b-363f-4d39-9819-2edb16acd2bb)" talking about how to efficiently mix Centralized BI and Self Service analytics.

https://twitter.com/FTisiot/status/1143627681579319296?ref_src=twsrc%5Etfw

Kscope is also nice since I always get the chance to see a lot of familiar faces from Europe and US!

https://twitter.com/rmoff/status/1143314891744223233?ref_src=twsrc%5Etfw


## July - Confluent Blog Post and Spatial Studio


The GoT post mentioned before was considered quite interesting around the globe and Confluent gave me the opportunity to write a Guest blog post. Taking the idea from mine I extended it to the [FIFA Women's World Cup,](https://www.confluent.io/blog/ksql-in-football-fifa-womens-world-cup-data-analysis) embedded the **Google Natural Language** **APIs** into **KSQL UDFs** and finally visualising the results with **Google Data Studio** querying** Google BigQuery.**

![Average Sentiment per Country](https://cdn.confluent.io/wp-content/uploads/Average_Sentiment_Per_Country_Map.png)
Working with the Confluent team was awesome, the whole experience, their feedbacks and their attention to details paid back by far all the time spent into creating the whole example + it was very fun and interesting!

July also meant the release of **Oracle Spatial studio**, a visual tool aimed at business users enabling them to perform deep spatial analytics! I wrote a blog post with an [example about it](https://www.rittmanmead.com/blog/2019/07/oracle-spatial-studio/)

![](https://www.rittmanmead.com/blog/content/images/2019/07/image-65.png)



## August - OAC Scaling and Balance Check with REST-APIs


August is traditionally a holiday season without a lot of conferences around. I used my time to write a couple of blog posts:



 	  * [OAC Row Limits and Scale Up or Down](https://www.rittmanmead.com/blog/2019/08/oac-row-limits-scale-up-or-down/): Oracle Analytics Cloud comes in several shapes (OCPU based) in order to fulfil customer needs. A detail that is not clear sometimes is that with each size you also get some [query limits](https://docs.oracle.com/en/cloud/paas/analytics-cloud/acsom/create-services-oracle-analytics-cloud.html#GUID-164D8568-9AE3-4A74-9F1A-0D87B78713C9), the blog defines them in detail and covers also how to scale up and down if needed
 	  * [Where is My Money Going? Checking the balance with Oracle Cloud Account Metering REST APIs](https://www.rittmanmead.com/blog/2019/08/oracle-cloud-account-metering-rest-apis/): Using the cloud is nice, you give your credit card details and boom, within minutes you have all set and ready. However this flexibility comes with a cost, you pay per hour/minute/second! To keep track of your cost in an automated way Oracle provides REST API calls, which are covered in detail in the blog post.

![](https://www.rittmanmead.com/blog/content/images/2019/08/image-24.png)



## September - OpenWorld!


I was honoured to be selected to speak at OOW19. My "[Become a Data Scientist](https://speakerdeck.com/ftisiot/become-a-data-scientist)" talk was accepted and over 100 people joined it!

https://twitter.com/FTisiot/status/1173956828029497346?ref_src=twsrc%5Etfw

I published a review of [OOW announcements](https://www.rittmanmead.com/blog/2019/09/oow-2019-review-general/) with a dedicated [deep dive for Oracle Analytics](https://www.rittmanmead.com/blog/2019/09/oow19-review-oracle-analytics-deep-dive/). This was followed by a later post about an enhancement in OAC which provides details about the [Maps Data Quality in the visualisations](https://www.rittmanmead.com/blog/2019/10/oac_v105_4_map_data_quality/).

Check out [OOW London 2020](https://www.oracle.com/uk/openworld/?source=:ad:pas:go:dg:RC_WWMK190411P00093:OOWEurope_DG_PaidSearch&mkwid=%7Cpmt%7Ce%7Cpdv%7Cc%7C&GOOGLE&oow+london&EAIaIQobChMI-978qNSy5gIVSbDtCh1IEAMFEAAYASAAEgLyvvD_BwE&gclid=EAIaIQobChMI-978qNSy5gIVSbDtCh1IEAMFEAAYASAAEgLyvvD_BwE&gclsrc=aw.ds) as I might appear there too!


## December - UKOUG TechFest


UKOUG is always the conference closing the year, this time it was in my company home town: Brighton! The change in the scope (only Tech now) and place (the Grand Hotel in Brighton) made the conference feel much more personal and enhanced the networking!

I was there with two talks: the "[Is it Corked: Wine ML predictions with OAC](https://speakerdeck.com/ftisiot/is-it-corked-wine-machine-learning-predictions-with-oac-474fe035-e821-4531-9cf4-62e3224cd622)" and the "[Become an Equilibrista: find the right balance in the analytics Tech Ecosystem](https://speakerdeck.com/ftisiot/become-an-equilibrista-find-the-right-balance-in-the-analytics-tech-ecosystem-dad5788b-363f-4d39-9819-2edb16acd2bb)" plus I did an appearance in Charlie Berger's Wine session too which included Oracle Autonomous Datawarehouse, Oracle Machine Learning and Oracle Analytics Cloud!

https://twitter.com/CharlieDataMine/status/1202016548703129601?ref_src=twsrc%5Etfw


## A Great Year is Almost Over


As you could imagine, 2019 was a busy year, lots of conferences and the daily work for clients too! For that I need to thank my company, [Rittman Mead](https://www.rittmanmead.com), that always supports me for conference trips and prep work. The ACE Director nomination was one of the peaks of the year followed by the OOW talk with lots of people assisting.

However the biggest highlights are something not mentioned in the above list but is related to what I find when I come back: my older boss

https://twitter.com/FTisiot/status/1175306523431624705?ref_src=twsrc%5Etfw

And my younger boss

https://twitter.com/FTisiot/status/1186970528873144320?ref_src=twsrc%5Etfw

Hope your 2019 was good as mine and wish you an even better 2020! (And you're still in time to register for [ITOUG Tech Days 2020](https://itoug.it/eventi/tech-days-2020/)!)
