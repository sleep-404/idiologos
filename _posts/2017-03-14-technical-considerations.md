---
layout: post
title: "Technical Considerations"
date: 2017-03-14 20:40:00 -0600
tags: jekyll pelican obelisk python ruby elixir erlang comments 
---
Happy Pi Day!

I've been quiet lately for a few reasons, the most significant of which is that I've been focusing on completing the online course, ["Functional Programming in Erlang"](https://www.futurelearn.com/courses/functional-programming-erlang), taught by Simon Thompson of the University of Kent, and hosted by FutureLearn. I may discuss that experience in a future post, but in this post I'd like to focus on my general plans for this web log.

Another reason I've been quiet is that I've been considering what to do about comments, and haven't had the time to implement them properly. I created this blog using Jekyll, a static site generator written in Ruby. A static site generator allows you to set a few configuration options, write posts in Markdown or some other non-HTML markup language, and maybe include a theme; and then, with a single command, it generates the HTML, CSS, and JavaScript for the site. There's no database on the back end storing information, and no server-side programming language generating web pages on demand. Static web sites are good for speed and security, but not so great for interactivity. No database means no storage for login details and comments.

So, I have about four options. The first is to stay with a static site generator such as Jekyll, and not allow comments. If anyone is interested enough to respond to one of my posts, they can send me an e-mail. This is the easiest option for me, as it doesn't require me to change anything. However, again it's not very good for interactivity. I sometimes post comments, but almost never send e-mails to bloggers.
 
The second option is to add Disqus or other externally-hosted comments to the site. This option would probably be almost as easy as doing nothing, but I don't like the idea of comments here being at the mercy of a third party.

The third option would be to use one of various methods devised for adding comments to static sites. These tend to be rather hackish, often involving e-mails or GitHub behind the scenes, and usually not automatic--not that posting comments without intervening moderation is necessarily a good thing, from my point of view; but I do understand that having comments held in moderation for hours, or even days, can be discouraging. Nevertheless, for the time being, I am considering these options most seriously.

The fourth option would be hosting my own comments offsite. However, I feel that kind of defeats the purpose of having a static site, and if I do decide to go for that option, I may as well host my entire blog on Heroku, AWS, or some other service for hosting dynamic sites and web applications.

In addition to Jekyll, I am also considering Pelican (Python) and Obelisk (Elixir) to statically generate this blog. If I move to a dynamic site, I have numerous options, including Rails (Ruby), Sinatra (Ruby), Django (Python), Flask (Python), Phoenix (Elixir), Zotonic (Erlang), Nitrogen (Erlang), and N2O (Erlang). Of course, I would need to move this site from Netlify to another host, as Netlify only hosts static sites.

Anyway, that's where I'm at now. My goal this week is to implement comments. By the time my next post appears, I should have comments enabled, one way or another.
