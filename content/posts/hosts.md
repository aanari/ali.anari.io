---
title: "Denylist using /etc/hosts"
date: 2023-02-21
draft: false
description: "Avoid time wasters"
tags: [productivity]
---

## Overview

There are many ways to [track time usage](https://support.apple.com/guide/iphone/view-your-screen-time-summary-iph24dcd4fb8/ios), [schedule breaks](https://pomofocus.io/), and otherwise stay on top of productivity tasks.

However, I found it far too easy to open links leading to other links, and ultimately spend way too much time on news and social media, even when my main priority was researching the solution to some technical problem. 5 minutes quickly became 30 minutes, which could even become 2 hours.

The chain of distraction was all too familiar:

1. Coworker sends a link or meme in work chat
2. Link goes out to Twitter or other social media
3. Trending hashtags or suggested articles pop up
4. Sharing and discussing it with others takes even more time

or:

1. Searching for technical documentation or debug error strings
2. Those websites contains links to other headlines
3. Those headlines redirect you to news sources
4. Those news sources redirect you to tabloids

My solution to this problem was to completely abolish my access of these "time-wasting" websites (News and Social Media), on all desktop or laptop devices that I work with or own. The crudest way was to simply edit the system `/etc/hosts` file to do so on each machine manually.

I am still allowed to consume this content from my smart phone or tablet; however I am carefully using screen tracking when doing so from those devices.

There are definitely more clever solutions and complete databases of websites that volunteers maintain online to manage this for you, such as [https://github.com/StevenBlack/hosts](https://github.com/StevenBlack/hosts). My criteria was that I wanted something very lightweight, portable, and fully under my own control, but I may try this unified plugin-based hostfile solution in the future.

I also realize that in theory, it's trivial to "cheat" by editing your hosts file repeatedly to remove, then re-add these entries during the work day. In practice though, the inconvience of having to do so has kept me honest.

## /etc/hosts

```
# News
0.0.0.0	abcnews.go.com
0.0.0.0	bbc.com
0.0.0.0	bloomberg.com
0.0.0.0	cbsnews.com
0.0.0.0	cnbc.com
0.0.0.0	forbes.com
0.0.0.0	ft.com
0.0.0.0	huffpost.com
0.0.0.0	latimes.com
0.0.0.0	nbcnews.com
0.0.0.0	news.ycombinator.com
0.0.0.0	nypost.com
0.0.0.0	nytimes.com
0.0.0.0	reuters.com
0.0.0.0	time.com
0.0.0.0	usatoday.com
0.0.0.0	usnews.com
0.0.0.0	washingtonpost.com
0.0.0.0	wsj.com
0.0.0.0 news.google.com
0.0.0.0 news.yahoo.com

# Social
0.0.0.0	facebook.com
0.0.0.0	instagram.com
0.0.0.0	linkedin.com
0.0.0.0	pinterest.com
0.0.0.0	reddit.com
0.0.0.0	snapchat.com
0.0.0.0	tiktok.com
0.0.0.0	twitter.com
0.0.0.0	whatsapp.com
```

**Caveat:** [YouTube](https://youtube.com) and [Twitch](https://twitch.tv) are still allowed, but that may change in the future if they end up hindering productivity.
