---
author: ftisiot
date: 2016-10-13 07:28:11+00:00
draft: false
title: Remove OBI12c Data Visualisation from OBIEE
type: post
url: /2016/10/13/remove-obi12c-data-visualisation-from-obiee/
categories:
- Analytics
- Tech
tags:
- datavisualization
- obiee
- obiee12c
---

For anybody interested in how to remove or add Data Visualisation (also known as Visual Analyser)  option to a specific role in OBIEE 12c, Oracle released [Doc ID 2102444.1](https://support.oracle.com/epmos/faces/SearchDocDisplay?_adf.ctrl-state=oxlq9igqe_9&_afrLoop=368341663992727) which gives the detail about the procedure using _WLST_ and the _RevokeDVPermission.py_ (or _GrantDVPermission.py_) that should be present in the OBIEE12c Installation Middleware home by default.
