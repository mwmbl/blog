---
title: "Indexing a billion pages"
date: "2023-12-23"
author: ["Daoud Clarke"]
---

It's two years since we launched [Mwmbl](https://mwmbl.org), the open
source, non-profit search engine, on Boxing Day 2021. A good time to
take stock of where we are and where we're going.

# We've indexed over 100 million pages

Thanks to our volunteers, who crawl the web using the [Firefox
extension](https://addons.mozilla.org/en-GB/firefox/addon/mwmbl-web-crawler/)
and [command line script](https://github.com/mwmbl/crawler-script),
we're crawling up to a million pages a day, as
you can see on our [stats page](https://mwmbl.org/stats/). There are
around 50-60 users crawling on an average day.

Given that Mwmbl is still relatively unknown, it seems plausible that
we can reach our target of crawling three billion pages a day, to
refresh the entire index in one month.

## Indexing a billion pages in 2024

Our index is currently still a titchy 40 gigabytes, so scaling this up
by a factor of 10 should be enough to reach this target, and is
achievable on the generously donated hardware we are currently
using. Going beyond this will require new hardware, so we may end up
building our own as [Marginalia Search](https://search.marginalia.nu/)
does. We think we will eventually need to index 100 billion pages in
order to meet our [search quality goals](https://book.mwmbl.org/page/roadmap/).

## Other crawling improvements planned for 2024

 - Fix [Search queue does not have enough URLs from top domains](https://github.com/mwmbl/mwmbl/issues/140)
 - Fix [SEO spam showing up in crawler](https://github.com/mwmbl/mwmbl/issues/141)

# Users can curate search results pages

This is a big part of our goal to be a _community driven_ search
engine. Instead of ranking being determined by a secret
profit-driven algorithm, rankings will be determined by user curations
as well as an open machine-learning algorithm trained on the user
curations.

We currently have 54 registered users who have curated 241 search
results pages.

## Curation plans for 2024

The curation user experience is still in an experimental phase. We
will continue experimenting throughout 2024 to find the best way for
users to contribute to improving search results. All suggestions
gratefully received.



