---
title: "Tracking user IDs in the crawler"
date: "2026-05-22"
author: ["Daoud Clarke"]
---

We've just deployed a change to the crawler: we now store the user IDs
of the users who crawled each URL in the index, along with a timestamp
of when it was last crawled.

## Why track user IDs?

Until now, we've required volunteers to go through a vetting process
before they can crawl. Tracking user IDs lets us remove that
requirement. The plan is that anyone can agree to the terms of service
and generate their own API key to start crawling straight away. (API
key generation isn't implemented yet, but this change is the
foundation for it.)

The other benefit is that if multiple users have crawled the same URL,
we can treat that as a signal that the result is more reliable.

If we do get bad actors — crawlers submitting fraudulent results — we
now have a way to deal with them:

1. A user flags a search result
2. We identify the crawler responsible, confirm the bad behaviour, and block them

## Why track the last crawled timestamp?

The timestamp will let us identify stale items in the index so we can
purge them. This isn't implemented yet, but having the data is the
first step.

If you'd like to get involved, join us on
[Matrix](https://matrix.to/#/#mwmbl:matrix.org).
