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

Hi, I'm Francesco and recently joined Aiven.io as Developer Advocate!

In this first blog post, we'll have a look at how to install and login to Aiven's resources using the command line client.

If you're new to Aiven or if you already know the platform but want to automate some tasks, the client allows you to perform the same tasks available on [Aiven's web console](https://console.aiven.io/) but from your preferred terminal.

<!--more-->


## Install the Aiven client

The first step is very simple, installing Aiven's cli!
It can be done (as per [documentation](https://github.com/aiven/aiven-client)) with

```
python3 -m pip install aiven-client
```

**Note**: you need to be on Python 3.6 or later to successfully work with `aiven-client`

If the installation was performed correctly, you should now be able to call `avn` commands from your terminal. To obtain an overview of commands and options you can simply execute `avn -h`

```
FT:Client ft$ avn -h
usage: avn [-h] [--config CONFIG] [--version] [--auth-ca FILE] [--auth-token AUTH_TOKEN] [--show-http] [--url URL]  ...

optional arguments:
  -h, --help            show this help message and exit
  --config CONFIG       config file location '/Users/francescotisiot/.config/aiven/aiven-client.json'
  --version             show program's version number and exit
  --auth-ca FILE        CA certificate to use [AIVEN_CA_CERT], default None
  --auth-token AUTH_TOKEN
                        Client auth token to use [AIVEN_AUTH_TOKEN], [AIVEN_CREDENTIALS_FILE]
  --show-http           Show HTTP requests and responses
  --url URL             Server base url default 'https://api.aiven.io'

command categories:

    account             Account commands
    billing-group       Billing-Group commands
    card                Card commands
    cloud               Cloud commands
    credits             Credits commands
    events              View project event logs
    help                List commands
    mirrormaker         Mirrormaker commands
    project             Project commands
    service             Service commands
    ticket              Ticket commands
    user                User commands
    vpc                 Vpc commands
```

## Autocomplete

Autocomplete allows you to get suggestion for any command in the `avn` suite just by pressing `TAB`. Installing it just requires you to add the `argcomplete` dependency with

```
python3 -m pip install argcomplete
```

Then add the following line to your `~/.bash_profile` file

```
eval "$(register-python-argcomplete avn)"
```

All set! Now just press `TAB` to get autocomplete suggestions when writing any `avn` command.

![autocomplete](/images/2021/01/avn_autocomplete_cmd_t1.gif)

## Login

With `aiven-client` and autocomplete installed, you can start playing with the `avn` command in the shell.
The first task to perform is the login: you can easily achieve it with the following command, replacing `<USERNAME>` with the email registered on Aiven's website.

```
avn user login <USERNAME>
```
You'll be now prompted for the password. In case of a successful login, you should receive a message like the below telling you that a new Access Token has been created and stored under `~/.config/aiven/aiven-credentials.json`.

```
FT:Client ft$ avn user login ftisiot@aiven.io
ftisiot@aiven.io's Aiven password:
INFO	Aiven credentials written to: ~/.config/aiven/aiven-credentials.json
```
The token will also be visible from [Aiven's Profile Authentication page](https://console.aiven.io/profile/auth)

![auth_token](/images/2021/01/Authentication_token.jpg)

If, on the other side, your login is invalid you'll see the below error message
```
ERROR	command failed: Error: {"errors":[{"message":"Authentication failed","status":403}],"message":"Authentication failed"}
```
## Enable `aiven-cli` Login when using "Sign in with Google"

Please note that if you are using Google OAuth Single Sign-On ("Sign in with Google" in [Aiven's console](https://console.aiven.io)), trying to login with the same credentials via the `aiven-cli` will result in a failure with an `Authentication failed` message.

The **Aiven Password** login method used by `aiven-cli` needs to be enabled in [Aiven's Profile Authentication page](https://console.aiven.io/profile/auth)

![login methods](/images/2021/01/login_methods_enabled.jpg)

Once enabled you'll be able to login with your account email and the defined Aiven's password from `aiven-cli`!

That's it, you're in! You can now create services, teams, profiles and even tickets! Follow this space for more!
Happy browsing Aiven's services!
