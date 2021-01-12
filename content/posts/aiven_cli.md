---
author: ftisiot
date: 2021-01-06 15:30:00+00:00
draft: true
title: First Steps within Aiven... the CLI!
type: post
url: /2021/01/15/aiven_cli/
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

New year, new life! As Developer Advocate at Aiven I'll get to touch a good variety of Open Source software like PostgreSQL or Apache Kafka. However, before accessing the tools, I wanted to understand how I can easily spin up and down the instances on Aiven using the console!

<!--more-->


## Install the Aiven client

The first step is very simple, installing Aiven's cli!
It can be done (as per [documentation](https://github.com/aiven/aiven-client)) with

```
python3 -m pip install aiven-client
```

Note: you need to be on Python 3.6 or later to successfully work with `aiven-client`

**Tip**: if you want to enable **autocomplete** for `avn` commands, install the `argcomplete` dependency with

```
python3 -m pip install argcomplete
```
and add the following line to your `.bash_profile`

```
eval "$(register-python-argcomplete avn)"
```

After doing that you can just press `TAB` to get autocomplete suggestions when writing a command

![autocomplete](/images/2021/01/avn_autocompletev2.gif)

## Login

Once `aiven-client` is installed, we can start playing with the `avn` command in our shell.
It's now time to login, we can easily do it with

```
avn user login <USERNAME>
```
And you'll be prompted for the password.

*Note*: logging in with username/password won't work if you use the "Google Account" method.
You can see the list of Authentication methods under you Aiven account in the console.

![login methods](/images/2021/01/login_methods.jpg)

To login with username/password you'll need to enable the **Aiven Password** as Authentication Method.
