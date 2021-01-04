---
description: First post where I briefly talk about how I came to building this website. I compare and talk about few static website creation tools.
title: Hello World
date: 2019-09-19
---

### Intro
These days people are so used to jam packing web pages with most bleeding edge front-end tech they completely forget that simplicity is king. For example if you open up Medium or any other popular blogging site and check the network tab, oh boy - you could send a couple of space ships to the moon with that much computing power (I'm not even going to mention all the annoying popups, trackers and adds).

I really appreciate how some people put in extra effort to make their sites easy to use while also keeping them minimal. I always wanted to do something similar myself. Quite recently I came across this [Hacker News post](https://news.ycombinator.com/item?id=19607169 "Hacker News post") which finally convinced me to break the hiatus.

In this first post, I'll talk about how I came about setting up this whole thing.

### Requirements
Making a simple site is actually quite easy - just grab a few `.html` and `.css` files, drop them on a server, and you're done. However, that won't suffice in the long run. For example if you plan on having a fancy header, footer and alike, you'll have to copy that content over and over again. Furthermore, it would be wise to compress that content to reduce the load for your readers. Doing all of this by hand is not fun.

So to achieve that, I need the following:
* As little JavaScript as possible - avoid support issues across different browsers.
* Static content compression tool - squeeze out as much performance as possible.
* Something for templating - avoid copying stuff.

### Tests
Having these three requirements I went on to search for the perfect tool which would cover my needs. Turns out the selection is quite wide. A site called [StaticGen](https://www.staticgen.com "StaticGen site") lists a bunch of them. So I went out and picked the ones that I liked and tried them out.

The first one I tried was [WebPack](https://webpack.js.org "WebPack site") as I've already had some experience with it. Soon I found out that it is quite hard to set it up properly. I was previously using it with React where all of this is done automatically. However, this time I had to set it up from scratch for my needs. To achieve that, I had to install millions of plugins and configure them in odd ways. In short, this was a chore. In the end however, I actually got a decent-ish setup, but I could not get rid of JavaScript in the resulting output. There would always be some magical `.js` files being included in my resulting `.html` which would include scrambled JavaScript module loading code and what not.

I wasn't happy about my first choice, so next I tried [Parcel](https://parceljs.org "Parcel site"). I was quite happy about this one initially. Basically install, and you're done. However, at the time I was testing it out, I ran into multiple bugs regarding content resolving (images, stylesheets, etc.). Turns out that during the time I was testing it, the maintainers had decided to do a complete re-write and ignore most pull requests for bug fixes. Some of those bugs were a deal breaker for me, so I skipped this one.

Finally, I decided to try out [Hugo](https://gohugo.io "Hugo site"). I was a bit hesitant on trying out a tool which is written in a language I've never used. However, I was very pleased with the results and decided to stick with it.

### Hugo
I won't go into much technical details as the documentation is quite good, so I think there's no reason for that. However, I'd like to mention a few things:
* I was surprised on how easy it was to install. I'm sitting on Ubuntu at the moment, so `snap install hugo` and done.
* Secondly, I was really happy to have literally zero JavaScript dependencies in my project. It's quite refreshing not having to be force fed with layers and layers of broken libraries.
* It includes tonnes of features, starting from built in syntax highlighting and finishing with full-blown commenting systems.
* The template engine is very flexible.
* Finally, it supports Markdown by default, which is really neat for writing articles.

The only gripe I have about it is the template resolution. It is very implicit. I think that documentation on this could still use some work, I found this part to be really confusing.

### Final notes
I'm quite happy with the current setup and the tooling choice. I'll continue to improve it and will get into more details later on.

Also, one thing that I didn't mention was that I've also setup automatic deployments. Basically `git push` and a new site is up after a few seconds. If you're curious about all those details, you can checkout the source on [GitHub](https://github.com/Edvinas01/edvinas-dev "Source on GitHub").
