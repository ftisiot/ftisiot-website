---
author: ftisiot
date: 2016-09-29 08:10:24+00:00
draft: false
title: OBI12c Performance Tiles PDF Print Error
type: post
url: /2016/09/29/obi12c-performance-tiles-pdf-print-error/
show_reading_time: true
tags:
- OBI12c;OBIEE12c;12c;performance;tiles;PDF;print;Error;
---

Hi All,

If you already migrated to OBI12c you could experience, when printing to PDF a dashboard or analysis including performance tiles that the layout is not what expected.

[caption width="300" id="attachment_37" align="aligncenter"]![screenshot-of-safari-29-09-16-09-55-51](/images/2016/09/screenshot-of-safari-29-09-16-09-55-51.png?w=600)
Original Dashboard Layout[/caption]
[caption width="300" id="attachment_36" align="aligncenter"]![screenshot-of-safari-29-09-16-09-56-10](/images/2016/09/screenshot-of-safari-29-09-16-09-56-10.png?w=600)
PDF Print[/caption]The good news is that this has been recognised as a bug and if you are on 12.2.1.0.0 there is a patch available "_Patch 22759083: BIEE 12C DASHBOARD PRINT TO PDF MISSING PERFORMANCE TILES FROM THE REPORT_".

If you already upgraded to 12.2.1.1.0 I guess a similar patch will be released soon.

Edit: the same patch is available also for 12.2.1.1.0
