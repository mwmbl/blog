---
title: "Indexing a billion pages"
date: "2023-12-23"
author: ["Daoud Clarke"]
---

It's two years since we launched [Mwmbl](https://mwmbl.org), the open
source, non-profit search engine, on Boxing Day 2021. A good time to
take stock of where we are and where we're going.

## We've indexed over 100 million pages

Thanks to our volunteers, who crawl the web using the [Firefox
extension](https://addons.mozilla.org/en-GB/firefox/addon/mwmbl-web-crawler/)
and [command line script](https://github.com/mwmbl/crawler-script),
we're crawling up to a million pages a day, as
you can see on our [stats page](https://mwmbl.org/stats/). There are
around 50-60 users crawling on an average day.

Given that Mwmbl is still relatively unknown, it seems plausible that
we can reach our target of crawling three billion pages a day, to
refresh the entire index in one month.

### Indexing a billion pages in 2024

Our index is currently still a titchy 40 gigabytes, so scaling this up
by a factor of 10 should be enough to reach this target, and is
achievable on the generously donated hardware we are currently
using. Going beyond this will require new hardware, so we may end up
building our own as [Marginalia Search](https://search.marginalia.nu/)
does. We think we will eventually need to index 100 billion pages in
order to meet our [search quality goals](https://book.mwmbl.org/page/roadmap/).

##' Other crawling improvements planned for 2024

 - Fix [Search queue does not have enough URLs from top domains](https://github.com/mwmbl/mwmbl/issues/140)
 - Fix [SEO spam showing up in crawler](https://github.com/mwmbl/mwmbl/issues/141)

## Users can curate search results pages

This is a big part of our goal to be a _community driven_ search
engine. Instead of ranking being determined by a secret
profit-driven algorithm, rankings will be determined by user curations
as well as an open machine-learning algorithm trained on the user
curations.

We currently have 54 registered users who have curated 241 search
results pages.

### Curation plans for 2024

The curation user experience is still in an experimental phase. We
will continue experimenting throughout 2024 to find the best way for
users to contribute to improving search results. All suggestions
gratefully received.

## Financials

Our finances are managed by [Open Collective](https://opencollective.com/mwmbl)
where we currently have funds of $733.36. Our estimated annual budget
is $752.36 and we have spent $174.49. The biggest expense was
purchasing a PyCharm professional license at $116.58, on top of that
we have a GitHib Copilot license (somehow we don't qualify for the
free version, despite being open source), and Mozilla's VPN so that we
can crawl the web without getting blocked.

Most of our finances come from one generous donor, we hope to have
more donors in the coming year.

## Incorporating

We are still currently unincorporated as an organisation. The amount
of funding we are getting is not enough to cover the costs of
accounting and other overheads involved in forming an official
organisation. The most likely way for us to incorporate is as a
[Company Limited by Guarantee](https://en.wikipedia.org/wiki/Company_limited_by_guarantee)
which is a standard vehicle for non-profits in the UK - see e.g.
[The Openstreetmap Foundation](https://osmfoundation.org/wiki/Incorporation).

## Team

Mainly still just me (Daoud) with help from the community. Please join
our [Matrix chat](https://matrix.to/#/#mwmbl:matrix.org) if you are
interested in helping out!

