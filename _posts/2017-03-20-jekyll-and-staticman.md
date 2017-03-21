---
layout: post
title: "Jekyll and Staticman"
date: 2017-03-20 13:51:00
tags: jekyll staticman comments markdown kramdown
---
Last week I enabled comments on this site, thanks to [Staticman](https://staticman.net/). After getting comments working, I realized the time stamp was exactly five hours ahead of my time which, accounting for daylight saving time, unsurprisingly works out to Universal Coordinated Time. I recalled that Jekyll had a timezone configuration option, so I just added **timezone: US/Central** to my **_config.yml** file, pushed the change to GitHub, and now times on my Netlify site are the same as those on my local system. However, while writing this post, I noticed that it wasn't being generated. On a hunch, I removed **-0600** (my offset from UTC) from the date of the post in the post header, and immediately it was generated. So you can either specify your offset from UTC in the post header, or specify your timezone in **_config.yml**, but don't do both. Now let's get back to configuring Staticman:

## Introduction

While configuring Staticman, I used several resources. A basic overview may be found on the Staticman site [here](https://staticman.net/docs/). There is an example site that uses Staticman [here](http://popcorn.staticman.net/) (GitHub repository [here](https://github.com/eduardoboucas/popcorn)). Also extremely helpful were two articles on Michael Rose's Made Mistakes blog [here](https://mademistakes.com/articles/jekyll-static-comments/) and [here](https://mademistakes.com/articles/improving-jekyll-static-comments/), as well as the Made Mistakes [GitHub repository](https://github.com/mmistakes/made-mistakes-jekyll) and the [configuration guide](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) for the Minimal Mistakes Jekyll theme (also created by Michael Rose, of course). I decided to keep things fairly simple for the first iteration of my comments implementation. I used some [YAML](http://yaml.org/) for Jekyll configuration files, as well as HTML and [Liquid](https://shopify.github.io/liquid/) for the comment form and comments [templates](https://jekyllrb.com/docs/templates/), but didn't bother to do anything fancy with JavaScript or CSS. I'm considering changing the blog's theme from the default Jekyll Minima theme to something else, and I probably won't mess with JavaScript or CSS until after I've done that.

## _config.yml 

I added the following lines to my **_config.yml** file, following the example of Made Mistakes:

{% highlight yaml %}
# Name of repository, for use with Staticman
repository: "anglus/idiologos"

# Staticman comments (see staticman.yml for configuration).
staticman:
  branch: "master"
{% endhighlight %}

Here **anglus** is my GitHub account name, and **idiologos** is the repository name. New comments will be merged with the **master** branch.

## staticman.yml

Next, I added a file named **staticman.yml** to my Jekyll site's root directory:

{% highlight yaml %}
# Name of the property. You can have multiple properties with completely
# different config blocks for different sections of your site.
# For example, you can have one property to handle comment submission and
# another one to handle posts.
comments:
  # (*) REQUIRED
  #
  # Names of the fields the form is allowed to submit. If a field that is
  # not here is part of the request, an error will be thrown.
  allowedFields: ["name", "email", "url", "message"]

  # (*) REQUIRED
  #
  # Name of the branch being used. Must match the one sent in the URL of the
  # request.
  branch: "master"

  # Text to use as the commit message or pull request title. Accepts placeholders.
  commitMessage: "Add new comment(s)."

  # (*) REQUIRED
  #
  # Destination path (filename) for the data files. Accepts placeholders.
  filename: "comment-{@timestamp}"

  # The format of the generated data files. Accepted values are "json", "yaml"
  # or "frontmatter"
  format: "yaml"

  # List of fields to be populated automatically by Staticman and included in
  # the data file. Keys are the name of the field. The value can be an object
  # with a `type` property, which configures the generated field, or any value
  # to be used directly (e.g. a string, number or array)
  generatedFields:
    date:
      type: date
      options:
        format: "timestamp-seconds"

  # Whether entries need to be appproved before they are published to the main
  # branch. If set to `true`, a pull request will be created for your approval.
  # Otherwise, entries will be published to the main branch automatically.
  moderation: true

  # Name of the site. Used in notification emails.
  name: "idiologos.netlify.com"

  # Notification settings. When enabled, users can choose to receive notifications
  # via email when someone adds a reply or a new comment. This requires an account
  # with Mailgun, which you can get for free at http://mailgun.com.
  #notifications:
    # Enable notifications
    #enabled: true

    # (!) ENCRYPTED
    #
    # Mailgun API key
    #apiKey: ""

    # (!) ENCRYPTED
    #
    # Mailgun domain (encrypted)
    #domain: ""

  # (*) REQUIRED
  #
  # Destination path (directory) for the data files. Accepts placeholders.
  path: "_data/comments/{options.slug}"

  # Names of required files. If any of these isn't in the request or is empty,
  # an error will be thrown.
  requiredFields: ["name", "message"]

  # List of transformations to apply to any of the fields supplied. Keys are
  # the name of the field and values are possible transformation types.
  transforms:
    email: md5
{% endhighlight %}

I used the Staticman sample config file [here](https://github.com/eduardoboucas/staticman/blob/master/staticman.sample.yml), but made some modifications using the Made Mistakes config file [here](https://github.com/mmistakes/made-mistakes-jekyll/blob/master/staticman.yml). I don't make use of all of the configuration options in the above file yet. Note that moderation is set to "true", which means that comments will not be posted automatically; instead I must approve a GitHub pull request in order to merge new comments into the site. Also note that your e-mail address, if you choose to enter it, is automatically hashed using MD5. I don't make use of it yet, but it could be useful for Gravatar or some such, or for e-mail notifications if I implement them in the future. 

## _includes/comments.html

I took a look at the way Made Mistakes implemented the commenting templates, as well as the Popcorn sample site. In the end, I decided the simplest way to implement the new comment form and the posted comments template was as a single template. I used the sample template in the Staticman documentation ("Step 3. Hook up your forms" [here](https://staticman.net/docs/)) as a base, and changed things around according to my taste. This is what I have currently for my comment form (HTML highlighted):

{% highlight html %}
<!-- Comment form -->
<h3>Post a comment:</h3>
<form method="POST" action="https://api.staticman.net/v2/entry/{% raw %}{{ site.repository }}{% endraw %}/{% raw %}{{ site.staticman.branch }}{% endraw %}/comments">
  <input name="options[redirect]" type="hidden" value="https://idiologos.netlify.com">
  <input name="options[slug]" type="hidden" value="{% raw %}{{ page.slug }}{% endraw %}">
  <div>
    <legend>Name</legend>
    <input name="fields[name]" type="text">
  </div>
  <div>
    <legend>E-mail (optional)</legend>
    <input name="fields[email]" type="email">
  </div>
  <div>
    <legend>Message (<a href="https://kramdown.gettalong.org/syntax.html">kramdown</a> markup allowed)</legend>
    <textarea style="width:100%" name="fields[message]" rows="12"></textarea>
  </div>
  
  <div>
    <legend><em>Comments will appear after moderation.</em></legend>
    <button type="submit">Submit Comment</button>
  </div>
</form>
{% endhighlight %}

This just a basic HTML form, with three Liquid objects: **site.repository** ("anglus/idiologos"), **site.staticman.branch** ("master"), and **page.slug** (the file name of the blog post, e.g. "jekyll-and-staticman" for this post). The important point here is that clicking the submit button sends an HTML POST command to **https://api.staticman.net/v2/entry/anglus/idiologos/master/comments**. In order to make the comment form show up on my posts, I added the following Liquid tag at the bottom of each post:

{% highlight liquid %}
{% raw %}
{% include comments.html %}
{% endraw %}
{% endhighlight %}

At some point I should probably add a post template that includes the comments template, so it's automatically included in each post. But I think I'll wait to do that until I understand better how Jekyll themes are organized. 

## GitHub configuration

Configuring my GitHub repository was very straightforward following the [documentation](https://staticman.net/docs/) on the Staticman site. The relevant portion of the documentation here is "Step 1. Add Staticman to your repository". I logged in to my GitHub account, navigated to **Settings --> Collaborators** under the repository for this site, and added the GitHub user **staticmanapp** as a collaborator. That done, I entered **https://api.staticman.net/v2/connect/anglus/idiologos** into my browser and saw a page that read simply "OK!".  

## Deployment and testing

With Staticman configured to handle comments, it was time to test. I pushed my changes to GitHub, waited for the comment form to appear on Netlify, and then posted a test comment. The comment appeared almost immediately as a pull request to my GitHub repository--the first pull request I've ever received, even if it was from a bot. I merged the request, and it appeared in a new **_data/comments/...** directory in my repository. Staticman also sent me an e-mail notification, but I didn't notice that until after I'd merged the comment.

## _includes/comments.html (again)

Now that I had a Staticman comment, I needed to integrate it into my post. As I had already included the **_includes/comments.html** file in my post, I decided to add the posted comments template to the same file. At first I placed the posted comments after the comment form, but as I have my comments sorted in chronological order, I felt it was more logical to place the posted comments first, and the comment form after the last posted comment. Here is the posted comments template (Liquid highlighted):

{% highlight liquid %}
<p>
<hr>
<p>

<!-- Static comments -->
<!-- Partly based on Made Mistakes' comments.html file: -->
<!-- https://github.com/mmistakes/minimal-mistakes/blob/master/_includes/comments.html -->
<!-- Also partly based on Eduardo Boucas' reviews.html file -->
<!-- https://github.com/eduardoboucas/popcorn/blob/gh-pages/_includes/reviews.html -->

{% raw %}{% if site.data.comments[page.slug] %}{% endraw %}

  <h3>
    {% raw %}{% if site.data.comments[page.slug].size > 1 %}
      {{ site.data.comments[page.slug] | size }}
    {% endif %}{% endraw %}
    Comments:
  </h3>

  <p>
  <hr>

  {% raw %}{% assign comments = site.data.comments[page.slug] | sort %}

  {% for comment in comments %}{% endraw %}
    <br>
    <legend><strong>{% raw %}{{ comment[1].name }}{% endraw %}</strong><legend>
    <em>{% raw %}{{ comment[1].date | date: "%B %d, %Y" }}{% endraw %}</em>
    <p>{% raw %}{{ comment[1].message | markdownify }}{% endraw %}
    <hr>
  {% raw %}{% endfor %}{% endraw %}
  <p>
{% raw %}{% endif %}{% endraw %}
{% endhighlight %} 

The portion in the **&lt;h3&gt;** tags displays "Comments:" if there is at least one comment, and "X Comments:" where X is the number of posted comments if there are two or more comments. The Liquid **assign** tag includes a sort filter. As the comments are stored in the form "comment-1234567890123.yml", where 1234567890123 is the time stamp, they will be sorted in chronological order, oldest first, newest last. The Liquid **for** loop takes (some of) the fields in each comment, and outputs them as HTML: name, date (in the form "March 20, 2017"), and the text of the comment. The comment text is passed through a filter which converts any Markdown syntax to HTML tags.

You might think that Jekyll, being developed by one of the founders of GitHub, and being the recommended static site generator for GitHub pages, would use [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/#GitHub-flavored-markdown). If you thought that, you would be wrong. Instead, Jekyll uses [kramdown](https://kramdown.gettalong.org/syntax.html). I am not well-versed in all of the differences between GFM syntax and kramdown syntax. However, one important difference between the two is in the syntax of fenced code blocks. Plain vanilla Markdown uses indentation to mark code blocks. GFM and kramdown both also have fenced code blocks that allow you to specify the language the code block is written in, which allows for proper syntax highlighting of the code. GFM uses [triple backticks](https://help.github.com/articles/creating-and-highlighting-code-blocks/#syntax-highlighting) (`) to begin and end a fenced code block, whereas kramdown uses [triple tildes](https://kramdown.gettalong.org/syntax.html#fenced-code-blocks) (~). The language may be specified on the same line as the opening backticks or tildes. 

One strange thing I noticed was that my comments section appears properly formatted if I only use opening **&lt;p&gt;** tags, but if I try to close my paragraphs with **&lt;/p&gt;** tags, then it breaks the formatting of my comments. I'm not sure exactly where the problem lies, but I suspect it has to do with the way kramdown converts the comment text to HTML. 

## Conclusion 

For now my comment form is missing some behavior that could be implemented with JavaScript. Also, the styling could be improved with some CSS. As I have mentioned above, I will probably work on that after I have switched to a different theme, if the theme is still lacking. I don't know how many Jekyll themes work with Staticman, or with plain vanilla HTML forms like I've used for Staticman. Obviously "Minimal Mistakes" does, but I will probably use another theme, and the other themes I've seen assume that if anything is being used for comments, it's Disqus. Another problem is what to do when a comment is submitted. For now the behavior is to dump you back on the home page. I believe I should be able to change that with a small modification to **_includes/comments.html**. Also as I mentioned above, comments are moderated. If comment moderation becomes too much of a barrier, or too much of a burden, I will look at integrating reCAPTCHA or some other solution for filtering spam, and then set moderation to "false".

Thank you very much to Eduardo Boucas for writing Staticman, and to Michael Rose for documenting it so well!

{% include comments.html %}
