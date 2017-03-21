---
layout: post
title: "Open for Comments"
date: 2017-03-17 16:59:00
tags: jekyll staticman comments 
---
Just a quick note before I get started: It was still March 14 in my time zone when I wrote my previous post, when I generated the static pages using Jekyll, and when I pushed the changes to GitHub, but it seems Netlify's infrastructure has its own idea of what time it is.

I have given some consideration to the comments problem and come to a decision. The options I've given consideration to include Disqus, Muut, HTML Comment Box, Remarkbox, GoTell, Isso, Pelican Comments, and Staticman. 

## Disqus

[Disqus](https://disqus.com/) is the obvious choice. It's common and free to use. However, I don't like the idea of requiring people to sign in to post a comment, or worse: assuming that anyone who wants to post a comment should already have a Disqus account. I might as well assume everyone who visits this site is using a particular operating system or web browser. Also, the Disqus business model is based on ad revenue. I don't begrudge them that, but I'd rather not clutter up my site with ads. 

**Pros:** Nobody ever got fired for incorporating Disqus in their site.

**Cons:** Requires login. Privacy issues. Ads. Comments controlled by a third party. 

I have also given some consideration to other similar services, such as [Muut](https://muut.com/) and [Discourse](https://www.discourse.org/). I found nothing to recommend Muut over Disqus. Discourse is open source, and comments are portable, but I would either have to host them myself or pay Discourse to host them for me. That's their business model, and I don't begrudge them that.

## HTML Comment Box

[HTML Comment Box](http://www.htmlcommentbox.com/) is a service for hosting comments through an HTML (and JavaScript) widget. It's very much like the above solutions except that it's free, except possibly for "heavy users". Comments are left "anonymously" (pseudonymously, really), with no e-mail address required. An HTML Comment Box would need to be included in each post. HTML Comment Box has several settings, and comments can be moderated using a Google account. The widget can be styled using CSS. In recent months a [vulnerability](https://labs.detectify.com/2017/01/18/stored-xss-ing-millions-of-sites-through-html-comment-box/) was discovered in HTML Comment Box, which the developer quickly patched when he was notified. I don't consider myself an expert on security, but I don't regard the discovery of the vulnerability as meaning that HTML Comment Box is necessarily less secure than other options. Indeed, the fact that the vulnerability was found so recently probably means that security is pretty high on the developer's list of priorities right now. I commend the developer for his quick response.

**Pros:** Free to use, at least for low-bandwidth users. No logins required. No ads.

**Cons:** Comments controlled by a third party, although migration is allowed.

## Remarkbox

[Remarkbox](https://www.remarkbox.com/) is another service for hosting comments through an HTML/JavaScript widget. The author of Remarkbox uses Pelican, a Python static site generator. Remarkbox is free to use, but requires a monthly fee for the ability to moderate comments. Instead of choosing a display name, commenters are distinguished by a random series of alphanumeric characters, such as "6r2RkmmH", generated from their e-mail address. After you post a comment, you receive a verification e-mail. If you don't verify your e-mail, then your display name appears as "unverified", rather than the random characters. Comments can be exported as JSON.

**Pros:** Free to use. No logins required. No ads.

**Cons:** Moderation is not free. Comments controlled by a third party, although exporting comments is allowed. Commenters can't choose a display name. 

## GoTell

[GoTell](https://github.com/netlify/gotell), just recently renamed from "Netlify Comments", is Netlify's own solution for adding comments to a static site. Setting up GoTell is a bit more involved than the previous options. You need to clone the Netlify Comments starter template and push it to your GitHub account, then visit Netlify to integrate comment threads into your site. I'm not opposed to doing any of that. However, one section on the GoTell README page, titled "Setting up the API", gave me pause, particularly the words: "You'll need to run the API on a server." If I understand this correctly, I would need to run GoTell on a separate server in order to integrate GoTell comments into my static site. If my understanding is correct, then I feel I might as well host my entire site on Heroku, Amazon AWS, etc.

**Pros:** Free to use. No logins required. No ads. Comments are controlled by the site owner.

**Cons:** Setup is a bit involved. Appears to require a separate host to process the comments.

## Isso

[Isso](https://posativ.org/isso/) is a commenting server written in Python that stores comments in a SQLite database file. Users may choose a display name or use the default "Anonymous". Name, e-mail address, and website URL are all optional; no login is required. Comments may be posted automatically or moderated according to the site owner's preference. Unfortunately for my purposes, Isso requires configuring a server external to my static site.

**Pros:** Free to use. No logins required. No ads. Comments are controlled by the site owner.

**Cons:** Setup is a bit involved. Requires a separate host to store the comments. 

## Pelican Comment System

The [Pelican Comment System](https://bernhard.scheirle.de/posts/2014/March/29/static-comments-via-email/) is specifically for use with the Pelican static site generator, which is written in Python. Choosing this solution would necessitate migrating my site from Jekyll to Pelican. The advantage of this particular solution is that it's intended specifically for static sites. Comments are e-mailed to the site owner in markdown format. The site owner copies the e-mails to a directory, from which they are automatically incorporated in the site the next time it is built.

**Pros:** Free to use. No logins required. No ads. Comments are controlled by the site owner. Doesn't require a separate host for storing or processing comments (if you don't count e-mail servers).

**Cons:** Setup is a bit involved. Site owner must copy comments manually (or set up a process for automatically importing them from a mailbox to a particular folder).

## Staticman

[Staticman](https://staticman.net/) is another solution intended specifically for use with static sites. Staticman operates as a GitHub bot. The site owner must add Staticman (GitHub account: staticmanapp) as a collaborator to the site repository. When a user posts a comment, the comment is sent to the Staticman API web server, which passes the comment along to the Staticman GitHub account. If moderation is not enabled, Staticman automatically pushes the comment to the site repository. If moderation is enabled, Staticman submits a pull request, and the site owner can choose whether or not to merge the comment. Obviously, if moderation is enabled, comments will not be posted automatically, and when they appear on the site depends on the site owner. Recently Staticman has added support for reCAPTCHA. This should help cut down on spam comments, but reCAPTCHA doesn't support all browsers. It does support the most recent versions of the most common browsers on the most common operating systems, so I'm not sure if it's that big of a deal; however, using reCAPTCHA is another obstacle to posting comments. 

**Pros:** Free to use. No logins required. No ads. Comments are controlled by the site owner. Choice between automatic posting and moderation. Doesn't require a separate host for storing or processing comments (if you don't count the Staticman API web server and GitHub).

**Cons:** Setup is a bit involved. Site owner must decide how to handle spam comments. May impact the time required to generate the site.

## Conclusion

The solutions that appeal to me most strongly are the self-hosted solutions and Staticman. For now I'll go with Staticman. I will likely need to learn more about Git/GitHub and Jekyll/Liquid, and to refresh my memory of JavaScript, HTML, and CSS to use it effectively, but accomplishing things like that, and writing about them, is very much the purpose of this site. 

**Addendum:** I said I'd implement comments with this post, and I intend to do so soon, but I've run into some trouble with Jekyll not generating posts. I've looked into various reasons for this happening. I don't think it's related to the timestamp, and I'm pretty sure it's not a browser cache issue, as I checked the directory where the new post should have been generated, and it wasn't there. Strangely, the problem seems to be fixed now, but what bothers me is that I don't know what I did to fix it. Maybe it was a time-related issue after all. Anyway, with that delay I'm just going to make this post and then implement comments afterwards, hopefully before the end of the week. 

{% include comments.html %}
