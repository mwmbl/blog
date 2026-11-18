---
title: "Optimize Your Mwmbl Crawler"
date: "2026-11-18"
author: ["Blue Heron"]
---

## What is a Crawler, Anyway?

Mwmbl uses "web crawlers" to trawl the internet and find interesting, useful, and safe sites to add to the searchable index.

Unlike large search engines, Mwmbl depends on the community to help it crawl the billions of pages that make up the internet. If you're technically inclined, you can [follow the instructions here](https://book.mwmbl.org/page/community/#running-the-mwmbl-crawler-with-docker-compose) to set up your very own Mwmbl crawler to help grow the index. It only takes a few minutes to set-up and get it running.


## Available Nerd Knobs

Now that you have the crawler up and running, you may have noticed that the docs mention two tweakable "nerd knobs": the `CRAWLER_WORKERS` and `CRAWLER_THREADS` parameters. What are these, and how do they work? In short:

- `CRAWLER_WORKERS` is how many processes (independent instances of the crawler) running
- `CRAWLER_THREADS` is how many concurrent web-requests each of those workers can make (e.g. `10` means "try to crawl 10 websites at once")

For example, a configuration of 3 `CRAWLER_WORKERS` and 10 `CRAWLER_THREADS` (also known as `3x10`) would try to access up to 30 sites at once.  Which raises another question: should you use `1x30`, `3x10`, `2x15`, or something else?

The answer is "it depends" and, to be honest, I don't have a full picture of how it affects the crawler. But thankfully, you don't need to know -- we made a practical, methodical tool to help you figure it out!

## Optimizing Crawler Runtime

How do we evaluate how good a crawler is working? At a high-level, there's a simple metric: **how many pages are we fetching per minute?** Using this as an approximation of "more means the crawler is working better," we can try different combinations of knobs to see what works best.

We provide a handy-dandy tool that does exactly this. From your `mwmbl` directory, run `./deploy/crawler/benchmark.sh` and wait for the results. (If you're on a VM, you can run this script instead: `curl -fsSL https://raw.githubusercontent.com/mwmbl/mwmbl/main/deploy/crawler/benchmark.sh | sh`)

By default, the benchmarker looks at processes from 1-5 and threads from 1-100, for a total of 20 combinations of parameters. It spits out a `benchmark-results-fetches-<date>.csv` file with the results. 

This process takes about 30-40 minutes *per combination*, so you might want to leave it running overnight and check the results back.

## Interpreting the Results

The optimizer will tell you what combination was the `WINNER` (e.g. 3x10). You can also open the benchmark `.csv` file and decide for yourself what configuration you want to use.  For example, the CSV may contain something like:

```csv
timestamp,processes,threads,warmup_seconds,duration_seconds,fetches_per_minute,status,error
2026-11-17T21:51:51+00:00,1,10,300,1800,564.8,OK,
2026-11-17T22:56:40+00:00,1,10,300,1800,507.4,OK,
2026-11-17T23:31:50+00:00,1,25,300,1800,691.4,OK,
2026-11-18T00:07:05+00:00,1,50,300,1800,770.7,OK,
2026-11-18T00:42:21+00:00,1,100,300,1800,569.5,OK,
2026-11-18T01:17:39+00:00,2,10,300,1800,1117.3,OK,
2026-11-18T01:52:56+00:00,2,25,300,1800,947.7,OK,
2026-11-18T02:28:17+00:00,2,50,300,1800,770.4,OK,
2026-11-18T03:03:52+00:00,2,100,300,1800,679.7,OK,
2026-11-18T03:39:08+00:00,3,10,300,1800,1197.0,OK,
2026-11-18T04:14:39+00:00,3,25,300,1800,932.3,OK,
2026-11-18T04:49:52+00:00,3,50,300,1800,833.0,OK,
2026-11-18T05:25:07+00:00,3,100,300,1800,752.7,OK,
2026-11-18T06:00:30+00:00,4,10,300,1800,1221.3,OK,
2026-11-18T06:35:50+00:00,4,25,300,1800,989.2,OK,
2026-11-18T07:11:09+00:00,4,50,300,1800,900.9,OK,
2026-11-18T07:46:28+00:00,4,100,300,1800,889.9,OK,
2026-11-18T08:21:40+00:00,5,10,300,1800,1704.3,OK,
2026-11-18T08:56:55+00:00,5,25,300,1800,1708.2,OK,
2026-11-18T09:32:10+00:00,5,50,300,1800,992.1,OK,
2026-11-18T10:07:24+00:00,5,100,300,1800,916.3,OK,
```

After the date and time, the colums are `CRAWLER_WORKERS`, `CRAWL_THREADS`, how long the test ran in seconds (1800 is 30 minutes), and the number of pages crawled per minute.

In the example above, you can see that a configuration of `1x10` on this machine would result in ~564 fetches per minute. But a configuration of `5x100` results in ~916  fetches per minute. (More than 5x the resources, but less than double the output!)

In this case, we can see `5x10` with a score of ~1704 and `5x25` with a score of ~1708. I would personally choose `5x10` since it's almost the same performance-wise but takes up less resources/bandwidth.

In your case, maybe you don't *want* Mwmbl to aggressively crawl as much as it can. Maybe you need to share internet with a lot of other people, so you prefer to crawl with less results per minute. That's OK. The choice is yours; use the data to decide.

Armed with these imperical results, open up your `.env` file, set `CRAWLER_WORKERS=5` and `CRAWL_THREADS=10`, restart it with `docker compose up -d` and enjoy!

## Wait, What? Why is 5x100 Slower Than 5x10?

"Wait!" you might say. "Why would 5x10 be slower than 5x100?" The answer is "it's complicated." While you may have 50 vs. 500 threads (things running on your PC trying to fetch websites), it's possible that:

- Your computer runs out of network resources with `5x100` so those same 500 threads are fighting each other; whereas `5x10` might not have that same contention.
- The bottleneck on your PC might not be "how fast can I access websites?" but "how fast can I process data?"
- You might have been downloading a lot of data while the 5x100 config was running (one of the reasons why we suggest running the benchmarks overnight)
- Running out of memory, CPU availability, network bandiwdth, or other shared resource on your PC
- A number of other things I can't fathom right now

This is why the benchmarker uses an *imperical* methodology: it gives you the actual, real, tested results.

## Conclusion

Mwmbl's crawler exposes a couple of nerd knobs and an imperical, systematic process for optimizing how many sites it can crawl/index at once. Using the data, you can decide how far you want to take the crawler, and how much bandwidth you want to use.

If you have any questions, concerns, or comments, hit us up in our Matrix server (Crawling channel).
