---
title: "Update July 2025"
date: "2025-07-21"
author: ["Daoud Clarke"]
---

It's been so long since we've had an update on the blog that people
are often confused as to whether the project is still active. It
definitely is! I'm just bad at updating the blog. Most of the updates
have been going to the Matrix channel.

So an update is long overdue.

Most of the recent work has been about making Mwmbl efficient
again. When we started, response times were well under 100ms, but over
the years, they've slowly crept up. There are several reasons for
this:

 - Our distributed crawler was placing significant load on the central
   server. Although the crawling itself was distributed, there was a
   lot of overhead in processing the crawled data for indexing and
   finding new URLs to crawl.
 - Badly written Django code 😬 that was causing [N+1 queries](https://stackoverflow.com/questions/97197/what-is-the-n1-selects-problem-in-orm-object-relational-mapping)
   on the database. 

The first of these has been the most difficult to fix, and required
rethinking the crawler completely. The [new command-line crawler](https://book.mwmbl.org/page/community/#running-the-mwmbl-crawler-with-docker-compose)
performs most of the tasks that were previously performed by the
central server. It has a local index and queue of URLs to crawl. It
then syncs its local index with the central one. The syncing process
is a very lightweight task for the central server, so once all our
volunteers switch to the new crawler, we will signifcantly reduce the
load.

The [Firefox extension](https://addons.mozilla.org/en-GB/firefox/addon/mwmbl-web-crawler/)
has also been completely rewritten and has a new purpose. It is now
used to build a dataset for evaluating Mwmbl ranking by scraping
Google search results. This will be important for our future plans...

The second problem was straightforward to fix once it was identified
(thanks to our Sentry logging).

## What's next?

### New front end

The focus now is on getting the new front-end ready to replace the
current one. This means implementing a new curation interface. The
plan is for this to be more like the familiar upvote/downvote
interface from sites like Stackoverflow. The backend for this is
implemented, and we have a design for the front end.

### Ranking algorithm leaderboard

With the new evaluation data from the Firefox extension, we plan to
implement a leaderboard website that allows anyone to upload a new
ranking algorithm implemented as WASM code. This will be evaluated
against our "gold standard", which for now will be the Google
rankings. Of course our goal is not to replicate Google, so our actual
ranking algorithm will not necessarily be the one that scores the
highest on the leaderboard, and will place more emphasis on user
contributions such as the existing curations and the new
upvote/downvotes.

## Finally...

Thanks for your support of Mwmbl. It means a lot. If you haven't
already, please consider joining our
[community on Matrix](https://matrix.to/#/#mwmbl:matrix.org) ❤️
