---
title: 'Fix Your Strings!'
date: "2023-03-07T16:33:17+01:00"
url: "/talks/fix-your-strings/"
event: "Pg Day Paris 2023"
location: "Paris"
site: "https://2023.pgday.paris/"
video: ""
slides: "/slides/fix-your-strings/fix-your-strings.pdf"
thumbnail: "/slides/fix-your-strings/cover.png"
image: "/slides/fix-your-strings/thumbnail.png"
description: ""
tags:
- postgresql
- string
- fuzzystrmatch
---

Strings are one of the most used types in databases; they can store pretty much any data and don't enforce any rules on the inserted input. This talk showcases the risk of using strings and how to mitigate them to ensure data quality.

<!--more-->

![Intro image](/slides/fix-your-strings/thumbnail.png)

## Abstract

Strings are one of the most used types in databases; they can store pretty much any data and don't enforce any rules on the inserted input. Yet too much freedom sometimes leads to inconsistencies: is it Aivan or Aiven? Øyvind or Oyvind? Wine or Whine? These seemingly small differences can have bad side-effects, causing lookups to fail and incorrect aggregation results to be returned. Luckily all is not lost: PostgreSQL has some features that can help us make sense of the chaos.

In this talk you will learn what PostgreSQL has to offer: starting with pattern matching, passing by regular expressions, and ending with more advanced functionality exposed by the fuzzystrmatch and unaccent extensions. I'll demonstrate what tools can help you fixing string inconsistencies and how to avoid making the same mistakes again in the future. This session is recommended for anyone who deeply cares about their (string) data quality.


## Useful Links


* [Array Functions](https://www.postgresql.org/docs/current/functions-array.html)
* [Date Functions](https://www.postgresql.org/docs/current/functions-datetime.html)
* [Pattern Matching](https://www.postgresql.org/docs/current/functions-matching.html)
* [Domain](https://www.postgresql.org/docs/current/sql-createdomain.html)
* [Email Domain](https://bit.ly/3ol6yfr)
* [Unaccent](https://www.postgresql.org/docs/current/unaccent.html)
* [FuzzyStrMatch](https://www.postgresql.org/docs/current/fuzzystrmatch.html)
* [Aiven](https://aiven.io/)
* [GitHub repository with the notebook](https://github.com/ftisiot/fix-your-strings)
