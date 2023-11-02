---
title: "November 2023 Update"
date: "2023-11-01"
author: ["Daoud Clarke"]
---

Highlights:
 - We now have 105 million pages in our index
 - We're crawling around a million pages a day
 - Around 60 people are helping to crawl the web each day
 - The beta version of Mwmbl now allows user curation of search results


# Side projects

My main problem with side projects is that I tend to stop working on
them. It feels like I've started so many things only to abandon them,
and that makes me feel bad. Over time, I've come to realise that this
is a feature, not a bug. I work on side projects because I _enjoy_
working on them. If I stop enjoying it, then I should stop working on
them.

The amazing thing about Mwmbl is that, with the exception of a few
long hiatuses, I have continued working on it, which means I'm still
enjoying it. I also really _care_ about the goals of the project, and
the little community we've built around it. The idea of a free and
open source search engine without ads still seems like something that
should necessarily exist.

Some of the breaks were for good reasons, like having a fourth child,
Layla, who is now six weeks old. Some of them were for spurious (but I
would still argue, good) reasons, like [inventing a sudoku-pentominous
puzzle
hybrid](https://logic-masters.de/Raetselportal/Raetsel/zeigen.php?id=000F6A).
But I have come back to Mwmbl, and I'm still having a great
time. There is also still much more to do...


# Background

I launched Mwmbl on Boxing Day 2021 with a [Hacker News
post](https://news.ycombinator.com/item?id=29690877). At the time
Mwmbl was little more than a proof of concept with a few hundred
thousand pages indexed. But the response, while not overwhelmingly
positive, was significant enough to keep me working on the project.

Shortly afterwards, [Colin Espinas](https://github.com/ColinEspinas)
joined and designed a beautiful front-end which is still in use today.

Slowly the [community](https://matrix.to/#/#mwmbl:matrix.org) grew, as
we launched the distributed web crawler that allows users to help us
crawl the web, either as a [Firefox
extension](https://addons.mozilla.org/en-GB/firefox/addon/mwmbl-web-crawler/)
or [python script](https://github.com/mwmbl/crawler-script).


# Howdy Mwmblers!

The best thing about this project has been the support we have
received from the community. Mwmbl runs entirely on a donated
server. Other generous donors have
[given](https://opencollective.com/mwmbl) over $800 to support the
project. Over ten people have contributed to the [source
code](https://github.com/mwmbl). And tens of our amazing volunteers
are crawling web, donating their compute and bandwidth. Without them,
we would have no search results.

So firstly I want to thank all of you that have contributed in any way
to the project.


# New things

It's been about a year since the last update. I'm sure there are
plenty of other new things, but here are some highlights.

## Launch of the stats page

A few weeks ago we launched a [real-time stats
page](https://mwmbl.org/stats/). On an average day, around 60
volunteers are helping us crawl the web, and we're crawling around a
million pages a day. We now have visibility on which domains we're
crawling most frequently, and ensuring these domains are
representative of what the community cares about is now a high
priority.

## Switch to Django

If you'd told me I'd be using Django on this project when I started
I'd probably have metaphorically slapped you. The [grug brained
developer](https://grugbrain.dev/) in me would have said "Complexity
bad! Django very complex. Mean Django very bad", and largely I would
still agree with that. But having seen Django used at work to build
some very cool things very quickly, I have been convinced that
sometimes the cost is worth it.

More importantly, for our goals for Mwmbl, the cost of _not_ using
Django is higher than the cost of using it. Django gives you a
sophisticated auth and user management system. This is something I
would rather not have to build myself, and it's necessary for our long
term goals.

So far, just the API on the main site is using Django (together with
the excellent Django Ninja), but we are slowly experimenting with
moving the front end to it, with [htmx](https://htmx.org/) for AJAXy
stuff.

## Launch of Mwmbl Beta

The reason we need auth and user management is because of the curation
tools we're building that will allow users to rerank, add and delete
search results. The vision is that this will ultimately be like a kind
of Wikipedia or Stack Overflow for search. Not every results page will
be manually curated, but the curated ones will give us insight and
statistics that will be used to automatically rank pages for other
results page, and yes, provide a dataset for [learning to
rank](https://en.wikipedia.org/wiki/Learning_to_rank).

I announced this idea a long time ago, and began working on it. After
falling into a few holes along the way, stopping for one of my
hiatuses and restarting again, I am finally happy to announce the
launch of [Mwmbl Beta](https://beta.mwmbl.org). It's not more than an
early prototype and the front end is horrible because I built it
myself. But the key things are there - you can rerank search results,
and add and delete new ones. Give it a try - just beware, this is an
early version and still needs much love and attention.


# Progress towards the Googlarity

The Googlarity is when Mwmbl is a lot better than
Google. Specifically, we should be:

 1. Faster
 2. Have better results
 3. Ad free

When we have all of these things, our target market will all
switch to using Mwmbl and we can rejoice.

We have had 1 and 3 on the above list since Mwmbl was launched, so
I'll focus mainly on 2, getting better results.

There are two essential components to having good search results:

 1. Having enough good pages in the index
 2. Being able to rank these pages effectively for a given search
    query

The first is essentially a problem of _crawling_: have we crawled
enough pages to be able to find pages that are relevant to the user's
query? The second is a question of _ranking_: given that we have the
pages in our index that are relevant to the user's query, what is the
best order to show them in?

Our early experiments showed at the time that the first problem was
the main hurdle to overcome. We simply did not have enough pages in
our index to satisfy users' queries. That is why we focused on
building tools for the community to help us crawl the web, rather than
improving our ranking algorithm, for example.

In order to achieve the Googlarity, we think we need around [100
billion
pages](https://www.kevin-indig.com/googles-index-is-smaller-than-we-think-and-might-not-grow-at-all/)
in our index, perhaps less. We are currently at around 100 million, so
only three orders of magnitude off!

The key, and the tricky bit, is to achieve this without having to
spend lots of money. Luckily, Mwmbl is designed to achieve this
goal. We are still happily running on a single server, and our plan is
to stay this way for the forseeable future.

## Index stats

The old method of finding out how many pages were in the index was to
query the URL table in Postgres and see how many had the status "crawled". However
this method has now become too slow to be workable, so I devised a new
method.

Since the index is only 40GB, it's easy to
[download](https://f004.backblazeb2.com/file/mwmbl-index/2023-10-28/index-v2.tinysearch)
locally and run analysis on. Using
[HyperLogLog](https://github.com/ascv/HyperLogLog) we can get a rough
count of the number of URLs without running out of memory.

As we were scanning the index I collected some other stats:
 - We have a total of 343036391 (non-unique) URLs in the index. This
   means that each URL is indexed around 3.3 times on average.
 - Each [page](https://book.mwmbl.org/page/architecture/#index-layout)
   has on average 33.5 items

This is reassuring since it means that the hypothesis that Mwmbl is
built on is holding up. Namely that, on average, most pages will rank
for only a few search terms.

There is scope to improve this further. We currently use a naive
method to choose which URLs to keep in each page - we just select the
highest scoring one, where the score is a heuristic based on
backlinks. If we also take into account the relevance of the page to
the search result, we can probably reduce the 3.3 times average above,
meaning we can store more URLs overall.

## Search quality

It's reassuring that the queries mentioned in the top comment on that
[first Hacker News post](https://news.ycombinator.com/item?id=29690877) at least _look_ a
lot better: [best car brands](https://mwmbl.org/?q=best+car+brands),
[what is a test?](https://mwmbl.org/?q=What+is+a+test%3F) and [duck duck go](https://mwmbl.org/?q=Duck+Duck+Go)
all look hugely improved.

Actual [evaluation results](https://docs.google.com/spreadsheets/d/1iJoQ0y0wD7_E8bEgS5Gv8VZCK8C9cfN5epkDwxypSlA/edit?usp=sharing)
tell a different story however - it seems things have gotten slightly
worse in the last year. This lends credence to my son's claim that the
results have never been the same since I accidentally [deleted the index](https://www.youtube.com/watch?v=wdXC3PAJRD0)...

Anyway, given that the size of our index is now a lot bigger, it's
probably a good time to re-evaluate the ranking algorithm.

# Things I worry about

 - The fact that I can't run queries against the URL table in Postgres
   is concerning. A good DB admin could probably fix it, but it ain't
   me babe.
 - The domains we are crawling. We are meant to be crawling Github,
   Wikipedia, StackOverflow etc, which we are, but we're also crawling
   diwaxx.ru and m.17ll.com. It turns out, deciding which URLs to
   crawl is a hard problem.
 - Building an institution. The Wikimedia Foundation is set up to last
   for hundreds of years. We should have something like that for
   Mwmbl.



# What's in a name? or - a rant

Mwmbl is pronounced "mumble", and no, it's not English, it's Welsh
([kind of](https://en.wikipedia.org/wiki/Mumbles)). And no, we're not
going to change the name because [you don’t like it](https://news.ycombinator.com/item?id=37564611) or can't remember
how to spell it.

As for being a marketing failure, you clearly don't understand the
remarkable marketing jiu-jitsu feat we have performed! Our target
audience is _people who like the name Mwmbl_, or more generally,
people who care more about the philosophy and technology behind the
project than the name, i.e. Hackers, Free Software afficionados,
and lovers of the Free Web. If you're reading this, it's probably
you - howdy Mwmbler!

But seriously, we have thought about changing the name many times, and
every time we decided as a community to stick to it. Perhaps if
someone donates the mumble.org domain name, we'll think about it
again.
