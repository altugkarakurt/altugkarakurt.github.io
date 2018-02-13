---
layout: post
title:  "Hosting a Jekyll Website: The Rookie Mistakes Edition"
date:   2018-02-12 19:08:32 -0500
categories: jekyll programming
tags:
  - programming
  - jekyll
  - web development
---

Inspired by the great <a href="https://blog.miguelgrinberg.com/index" target="_blank">Miguel Grinberg</a>, who built a blog with Flask to blog about how to start blogging with Flask, I wanted to write a little but about my experience with Jekyll. By the way, if you want to learn about Flask framework, Python web development or web frameworks in general, I can't recommend Grinberg's blog enough. Ok, back to Jekyll.

#### Why Jekyll? ####
Well, I had a Wordpress website running on a DigitalOcean droplet for almost two years. All that time it felt like a chore to update or customize the website because of Wordpress. For some reason, I don't feel comfortable leaving the control to automated tools without understanding what is going on under the hood first.That's why, I decided to get rid of that website and stop paying monthly server fees for a constant sense of guilt hanging over my head and move to GitHub pages with a lower level content management system. Jekyll is almost the undisputed default for this purpose, so here we are. 

#### My Experience with Jekyll ####
I was initially a little reluctant because I knew Jekyll was running on Ruby and I don't know anything about Ruby. Thankfully, unlike Flask or Django, Jekyll only expects you to work on templating and creating Markdown content to fill the pages. As you can see I came a log way without a single line of Ruby and I doubt I would ever need to use Ruby for maintaining website. If you are thinking about switching to a lightweight content manager, you can't go wrong with Jekyll. A little HTML/CSS knowledge helps, but it is possible to maintain a Jekyll page without a single line of code.

If you're looking for a good place to start, you can't wrong with <a href="https://www.youtube.com/watch?v=T1itpPvFWHI&list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB" target="_blank">these</a> video tutorials. Below, I will list the simple mistakes that I made along the way as pointers to other newbies as well as reminders for myself. I will keep updating this page and adding new items to the list as I go. Hopefully, it will not be much longer than it is right now.

#### Rookie Mistakes That I Made ####

* Installation process has not been as smooth as I would've liked. I am running Ubuntu on my laptop and I think partly because of Ubuntu's lack of native Ruby support, some of the packages were a little tricky to install. You are mostly going to use `gem`, a Ruby package manager to install some packages, but don't be surprised if you get uninformative fatal errors in the process. Googling the error messages and skimming through a few Github issues or ubuntuforums posts will guide you to the right direction. I had to install some dependencies through `apt-get` and you might need to go down to OS level a few times, too. It's not too bad, be patient.

* After initializing, you start your server for the first time using the command `bundle exec jekyll serve` and afterwards, you can simply run `jekyll serve` and it will generate the up-to-date website. That is, unless you made some changes to your `_config.yml` file, which hosts global attribites about your website. Don't forget to run the full command after editing your config file or you will waste many minutes trying to find non-existing errors because your changes will not affect the website.

* When I first generated my website, I had the default placeholder post in the directory, too. After making the changes and being satisfied with all the pages in my local website, I pushed it to GitHub without any posts. This ended caused the html templates to not be rendered properly and I ended up with a plain-text website. It took me a minute to figure out this was because some of the html templates iterate over posts in _posts folder and they were failing because it was an empty directory. Solution? Put in a placeholder post without no links to it and everything was running smoothly. This post is going to replace that placeholder. This is probably because of the template that I was using, but if for some reason you end up with a plain-text website, make sure you have at least one post in your _posts folder.

To be continued...