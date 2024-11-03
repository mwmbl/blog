---
title: "Re-ranking search results on the client side"
date: "2024-11-03"
author: ["Daoud Clarke"]
---

By many measures, [Mwmbl](https://mwmbl.org) is doing great. We have
indexed over half a billion pages, we have over 4,000 registered
users, and over 30,000 curations from those users. Our volunteers are
crawling around 5 million pages a day.

But the score that I care about most right now is
[NDCG](https://en.wikipedia.org/wiki/Discounted_cumulative_gain). This
measures the quality of our search results against a "gold standard"
which is just Bing search results for the same query. Obviously, we
are not ultimately aiming to be like Bing, so eventually we will stop
using Bing and start using our curated data, once we have enough and
the quality is high enough. But we are far enough away from being good
that moving in a Bing-like direction is great, for now.

Because our NDCG score is pretty poor. A score of 1 would be "matches
Bing exactly", while a score of 0 would be "nothing in common with
Bing". We are scoring 0.336 on our test set. However most of that
comes from sticking Wikipedia results at the top, by querying
Wikipedia with their [excellent
API](https://www.mediawiki.org/wiki/API:Search) and using their
ranking. Without that, we were scoring 0.059. Using Wikipedia alone,
we would score 0.297.

I've experimented off and on with [learning to
rank](https://en.wikipedia.org/wiki/Learning_to_rank). This was the
industry standard for ranking before large language models, so seemed
like an obvious place to start. Implementing a lot of [standard
features](https://www.microsoft.com/en-us/research/project/mslr/)
improved the results slightly over my original intuitively defined
features, but still only gave us an NDCG score of 0.365.

Nevertheless, after trying for a while to improve on this further, I
decided to deploy this ranking instead of the heuristic that we were
using before. It didn't seem to make the results worse, and it seemed
to be fast enough, at least on my local machine.

I didn't realise it at the time, but this was when things started
breaking. You see, we only have one server. We don't do actual
crawling on the server since that is done by volunteers. But we have
to process the crawl data to find new links, prioritise those links
for crawling, preprocess the data for indexing, and index it, as well
as serving search results. All on one server.

And it turned out that adding a ton of features and using XGBoost for
every search query added enough load to the server that things started
to slow down. Even loading the main page could take three or four
seconds. At the time, I didn't realise that this was causing the
problem, since a bunch of other stuff was happening. We had some
enthusiastic volunteers that were crawling many millions of pages a
day.

Search got so slow that I decided we had to turn off the crawling. I
made the server that sends batches to crawl return an empty list. I
turned off the scripts that update the crawl queue and index new
pages. And things got better.

But they still weren't as good as they were before. It took me a long
time to realise that it was the change in ranking that was the
problem.

In retrospect, we should have had better monitoring around everything
so that we could more easily identify the cause of the slowdown. But
this is a project that I do for fun, so I've put off doing this kind
of thing, because I find it boring. Boring is not fun. But a broken
website is also not fun. You live and learn.

Now I've changed the ranking back to the old heuristic and turned on
the crawling again. I still think learning to rank has potential. But
we can't afford it with our current resources. So I've started looking
at alternatives.

What would make ranking almost infinitely scalable? If we don't do it
ourselves, but get our users to do it, on the client side. This works
really well for us, because we don't rank like a normal search
engine. Our results are already ranked for each unigram and bigram
query. We pull out the pre-ranked results for each unigram and bigram
in a user's query, then re-rank them using our heuristic. This is
perfectly feasible to do on the client side, since we don't have more
than around 30 results per unigram or bigram on average.

It also gave me the final push I needed to implement ranking in
Rust. I know that Python is a bad choice for a search engine, but I
chose it because I knew I could build something quickly, and that I
would probably give up at some point if I tried to do it in Rust. Yes,
I know Java and C++, which would have been fine choices, but they
would not be fun. This project has to be fun or it will not happen.

I am a beginner in Rust, and doing too hard things at the same time -
building a search engine and learning Rust - seemed like a recipe for
disaster. But I can build small bits in Rust, especially if I have
already built them in Python. So over the last few days, I've [rebuilt
our heuristic ranking in Rust](https://github.com/mwmbl/rankeval/).
The rust compiles to web assembly which will eventually be called from
our [excellent new front end](https://alpha.mwmbl.org/). Now all the
back end needs to do is pull out pre-ranked results for each unigram
and bigram. These can be easily cached, and potentially even kept in
cloud storage. If we are ever going to serve millions of users on our
shoe-string budget, then this is how we will have to do it.

If you would like to get involved, join us on our
[https://matrix.to/#/#mwmbl:matrix.org](Matrix server), or send a pull
request to fix my rubbish Rust code. Thank you for reading!
