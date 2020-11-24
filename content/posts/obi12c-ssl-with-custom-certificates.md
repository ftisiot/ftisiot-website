---
author: ftisiot
date: 2016-10-16 18:40:45+00:00
draft: false
title: OBI12c SSL with Custom Certificates
type: post
url: /2016/10/16/obi12c-ssl-with-custom-certificates/
---

For anybody on OBIEE 12c wanting to enable full SSL, Oracle provided within the [OBIEE Documentation](https://docs.oracle.com/middleware/1221/biee/BIESC/ssl.htm#BIESC374) an explanation on how to set it up using demo certificates.

However the usage of demo certificates gives the end user the annoying problem of needing to accept an exception to enter the website since the demo certificate wasn't created for the server/domain where OBIEE is located (the same problem arises also with Smartview).

The problem is solved by using custom certificates, generated on purpose by a certificate authority. So far the usage of custom certificates with OBI12c was somehow hidden in the [Weblogic 12c Documentation](https://docs.oracle.com/cd/E24329_01/web.1211/e24422/ssl.htm#SECMG387), but now Oracle explained the process in [Doc ID 2188982.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=370404015522156&id=2188982.1&_adf.ctrl-state=oxlq9igqe_194) with a detailed list of steps.
