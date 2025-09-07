---
layout: post
title: "Memory Hole"
date: 2025-09-06
---

## Original Post
Just a quick note, to say I want to stop giving the effort I spend helping people use QGIS to Reddit to sell to the AI overlords. So I'm purposing my heart to put anything that I feel is useful on my blog, Even if it is published on GitHub (owned by one of the AI overlords), at least I control the domain and keep a copy of the information on my machine.

Problem, can I remember how this blog works? It's been almost 6 years since I posted anything. I guess if this is online, I remembered.

Things I'm getting hung up on:
+ What is the name of the software I'm using? Oh it's *Jekyll*.
    + Seems to be installed.
    + Try:
	
	  `bundle exec jekyll serve`
	+ Nope:
	
	  `Could not locate Gemfile or .bundle/ directory`
    + Google some stuff.
    + Try:
	
      `gem install jekyll bundler`
	+ Hmm, that barfed with:
	
	  ```
	  ERROR:  While executing gem ... (Gem::FilePermissionError)
	  You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
	  ```
+ Well, maybe I don't need to get it working locally, push it up to GitHub and see if anything happens.
    + Oh shit, git is still configured to use my password, which GitHub stopped supporting years ago I think.
    + Dink around look how something I'm using now is configured.
    + Hmm, still problems. Can't find repository.
    + Oh yeah, for some stupid reason I have this repository under a different GitHub username.
+ Hey I think that works, but I still need to have a local preview working.

## Update 07 Sep 2025

I have a new computer since I last used *Jekyll* and I was obliviously trying to use the system install of Ruby which is [not recommended](https://jekyllrb.com/docs/installation/macos/). Instead I've now installed Ruby 3.4.1 and chruby to manage my Ruby environment. In the environment I ended up with the command to serve a local version of my site is simply:

`jekyll serve`
