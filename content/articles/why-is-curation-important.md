---
title: "Why is curation of web search results important?"
date: "2023-11-30"
author: ["Daoud Clarke"]
---

[Mwmbl](https://mwmbl.org) is the first search engine to allow users
to change the search results:

![Mwmbl screenshot](/images/mwmbl.gif)

You can add results, delete them, and rerank them. The changes you
made are saved instantly to the index and will be shown to other users
who run the same query.

But what is the point of users changing search results? There are far
too many queries to expect them all to be curated by users.

The answer is that we can learn from users' curations. For example, we
can learn which sites are generally ranked higher than others, for
example if MDN is ranked above w3schools. More generally, we can use
machine learning to rank results, with curated searches as a training
set. This is called [learning to rank](https://en.wikipedia.org/wiki/Learning_to_rank).

By curating search results, you are really training Mwmbl in how we
should rank pages.

There are still a lot of things to figure out, most importantly, how
do we define what is a good ranking, and how do we reach consensus
around a ranking?

At the moment, one person can change the results for everyone
instantly, following the model of Wikipedia. But perhaps we will need
some kind of voting system to make it easier to resolve disagreements,
perhaps with upvoting and downvoting like StackOverflow.

The important thing though, is that the community determines the
ranking. In commercial search engines, the ranking is determined by
what will make the search engine the most money (and of course,
normally includes advertising). As a non-profit, we don't have that
constraint, so we can provide a better service to users. Well, that's
the theory, anyway!
