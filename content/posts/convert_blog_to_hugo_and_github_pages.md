---
author: ftisiot
date: 2020-11-26 11:00:00+00:00
draft: true
title: Convert Blog from Wordpress Hugo and GitHub Pages
type: post
url: /2020/11/26/convert-blog-from-wordpress-to-hugo-and-github-pages/
image: "/images/2020/10/talking.png?w=300"
show_reading_time: true
categories:
- blog
- hugo
- wordpress
- github
tags:
- blog
- hugo
- wordpress
- github
- github pages
- exitwp
---

I've had this small personal website for some time now, adding from time to time some stuff mainly related to Oracle Analytics, the [Italian Oracle User Group](www.itoug.it) and personal speaking experiences.

I was running it on Wordpress.com which was providing a nice completely managed experience: I didn't have to care about hosting, versioning, SSL etc, all was managed by the platform.
I just had to focus on content writing and on top of it I had, out of the box, nice dashboards showing visitors of the page and other blog stats.

As for everything in the cloud, however, also the above service comes with a cost. The more features I wanted, the more I had to be willing to pay:
* do you want premium Theme? pay extra!
* do you want more storage? extra!
* do you wanna custom plugins? extra!

The latter bit me recently, for some plugins I was looking at, Wordpress.com was asking to upgrade my site to "Business" plan. Paying 20£ per month wasn't an option for my personal blog and the amount of content I produce in here, so I started analysing and evaluating my next step.

Managed platforms are awesome, but do I want to jump from one to another with the risk of hitting another paywall sooner or later? And if I want to run away from managed platforms, do I have the willingness and time to manage properly my own own infrastructure?
Well, in my case is no... I don't want to spend time on any updates, security problems, SSL certificate renewal. In this cloud era, I just want a service giving me a basic way of managing a reliable website where I can easy add content using my day to day tools of choice and... as cheaply as possible.


This is why, after reading my previous colleague [Robin Moffat's experience](https://rmoff.net/2018/12/17/moving-from-ghost-to-hugo/) I started thinking about moving to Hugo and GitHub Pages. Hugo is perfect for static HTML websites which can be stored in GitHub pages doing the hosting. I leave you to [Robin's perfect explanation](https://rmoff.net/2018/12/17/moving-from-ghost-to-hugo/) on how to set it up, since my incarnation is a customised copy of his.

I wanted to add my 2 cents on the story, so I thought that clarifying a couple of steps I had to do from my side.

## Converting Wordpress to Hugo
My starting point was Wordpress, you can easily download all your content and images via the Wordpress.com GUI
![wp export](/images/wp-export.jpg)

Doing that, you'll download a zip file named `<username>.wordpress.com-YYYY-MM-DD-HH_mm_ss.zip` containing a single `<username>.wordpress.com-YYYY-MM-DD-HH_mm_ss.xml` file.
Hugo's website [migration page](https://gohugo.io/tools/migrations/) offers a variety of solutions to migrate to Hugo from various sources, including Wordpress. My tool of choice has been [exitwp-for-hugo](https://github.com/wooni005/exitwp-for-hugo), a simple python script.
Using it is pretty simple, first of all clone the repo

```
git clone https://github.com/wooni005/exitwp-for-hugo.git
```

then install the required libraries

```
sudo pip install --upgrade -r pip_requirements.txt
```

Place the `<username>.wordpress.com-YYYY-MM-DD-HH_mm_ss.xml` file under the `exitwp-for-hugo/wordpress-xml` folder and finally execute from the `exitwp-for-hugo` directory

```
./exitwp.py
```

The output is stored under the `exitwp-for-hugo/build/hugo` folder.
The rest of the exercise is basically to move the content from `exitwp-for-hugo/build/hugo` subfolders into the proper Hugo's location (e.g. for posts from the `_posts` subforder to Hugo's `/content/posts`)

Optionally you may want to change the generated filename for posts, since the tool stores them as `YYYY-MM-DD-<blog_name>.markdown` and I renamed them to `<blog_name>.md`
