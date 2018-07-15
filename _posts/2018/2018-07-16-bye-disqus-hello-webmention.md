---
title: Bye Disqus, hello Webmention!
date: 2018-07-16 09:00:00 -0700
tags:
  - indieweb
  - disqus
  - webmention
---


<img src="https://webmention.io/img/webmention-logo-380.png" style="float: left; padding: 0 10px 10px 0" />

Since 2013 I've used [Disqus][1] on this website for comments. Over the years
Disqus has been getting 'fatter', so I've been thinking of switching to
something new.

Then on Friday, I saw a tweet which got me inspired:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">I moved to distributed, standard based, and <a href="https://twitter.com/indiewebcamp?ref_src=twsrc%5Etfw">@indiewebcamp</a> solutions based comments, AKA WebMentions:<a href="https://t.co/WBbLctAn9V">https://t.co/WBbLctAn9V</a></p>&mdash; Nicolas Hoizey (@nhoizey) <a href="https://twitter.com/nhoizey/status/1017828818441134087?ref_src=twsrc%5Etfw">July 13, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

This links to [Nicolas Hoizey's blog][9], in which he details moving his
Github Pages-based blog from Disqus to Webmentions. I spent all day Saturday
to do the same.


What are webmentions?
---------------------

Webmentions is a [W3C standard][2] for distributed commenting. It's very
similar to "Pingbacks". When somebody wants to respond to an article here
from their own blog, a link will be created automatically.

I used the [Webmention.io][3] hosted service to do this. To receive
webmentions, I just needed to embed the following in the `<head>` of this
site:

```html
<link rel="pingback" href="https://webmention.io/evertpot.com/xmlrpc" />
<link rel="webmention" href="https://webmention.io/evertpot.com/webmention" />
```

The webmention.io site has a simple API, with open CORS headers. I wrote a
custom script to get the webmentions embedded in this blog. [source is on
github][4].


Importing old comments
----------------------

I exported the disqus comments, and wrote a script to convert them into JSON.
The source for the exporter is reusable. I also [put it on github][5] if
anyone finds it useful.

The last time I switch blogging systems I used Habari, but also never got
around importing comments. I took the time to import those as well, so now
comments all the way from [2006 are back][6]!

Jekyll has a 'data files' feature, which allows me to just drop the json file
in a `_data` directory, and with a recursive liquid include I can show comments
and threads:


```
// _includes/comments.html
<ul>
  {% for comment in include.comments %}
    {% include comment-single.html comment=comment %}
  {% endfor %}
</ul>
```

```
// _includes/comment-single.html
<li>
  {% if include.comment.url %}<a href="{{ include.comment.url }}">{% endif %}
  {% if include.comment.avatar %}<img src="{{ include.comment.avatar }}" alt="{{ include.comment.name }}" />{% endif %}
  {% if include.comment.url %}</a>{% endif %}

  {% if include.comment.url %}<a href="{{ include.comment.url }}">{% endif %}
  <span class="author">{{ include.comment.name }}</span>
  {% if include.comment.url %}</a>{% endif %}
  • <time>{{ include.comment.created | date: "%b %d, %Y" }}</time>

  {{ include.comment.message }}

  {% if include.comment.children %}
    <ul>
      {% for child in include.comment.children %}
        {% include comment-single.html comment=child %}
      {% endfor %}
    </ul>
  {% endif %}
</li>
```

And then lastly, to include it in the layout:

```
{% assign comments = site.data.comments[page.id] %}
{% include comments.html comments=comments %}
```

Getting tweets and likes from twitter
-------------------------------------

To get mentions from social media, like Twitter, I'm using [Bridgy][7]. This
is a free service that listens for responses to tweets and converts them to
Web mentions.

It also supports other networks, but Twitter is the only one I have setup. To
see it in action, you can see a twitter follow on [this blogpost][8].


What's missing?
---------------

It's not easy currently to discover on this site that Webmentions are
possible, and it's it's not possible to leave a regular comment anymore. I
hope I can fix both of these in the future.

Webmention.io does not have good spam protection. Spam was a major issue with
pingbacks, and is pretty much why pingbacks died. Webmention is not big enough
for this to be an issue, but if Webmentions grow, the spam issue has to be
solved.

Lastly, this blog does not yet do outgoing Web mentions. There's no obvious
way to do this with a static site generator either, so my intent is to write a
small crawler that takes links from blogposts, finds webmention links and
automatically call their webmention api.

I intend to just run this crawler manually when I publish new posts.

[1]: http://disqus.com/
[2]: https://indieweb.org/Webmention
[3]: https://webmention.io/
[4]: https://github.com/evert/evert.github.com/blob/master/js/webmentions.js
[5]: https://gist.github.com/evert/3332e6cc73848aefe36fd9d0a30ac390
[6]: https://evertpot.com/70/
[7]: https://brid.gy/
[8]: https://evertpot.com/http/201-created
[9]: https://nicolas-hoizey.com/2017/07/so-long-disqus-hello-webmentions.html