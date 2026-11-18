---
title: "Optimize Your Mwmbl Crawler"
date: "2026-11-18"
aliases: ["/articles/optimize-your-mwmbl-crawler/"]
author: ["Blue Heron"]
---

## What is a Crawler, Anyway?

Mwmbl uses "web crawlers" to trawl the internet and find interesting, useful, and safe sites to add to the searchable index.

Unlike large search engines, Mwmbl depends on the community to help it crawl the billions of pages that make up the internet. If you're technically inclined, you can [follow the instructions here](https://book.mwmbl.org/page/community/#running-the-mwmbl-crawler-with-docker-compose) to set up your very own Mwmbl crawler and help grow the index. It only takes a few minutes to get it running.

## Available Nerd Knobs

Now that you have the crawler up and running, you may have noticed that the docs mention two tweakable "nerd knobs": the `CRAWLER_WORKERS` and `CRAWL_THREADS` parameters. What are these, and how do they work? In short:

- `CRAWLER_WORKERS` is the number of crawler processes (independent instances of the crawler).
- `CRAWL_THREADS` is the number of concurrent web requests each worker can make (e.g., `10` means "try to crawl 10 pages at once").

For example, a configuration of 3 `CRAWLER_WORKERS` and 10 `CRAWL_THREADS` (also known as `3x10`) would try to fetch up to 30 pages at once. Which raises another question: should you use `1x30`, `3x10`, `2x15`, or something else?

The answer is "it depends," and, to be honest, I don't have a full picture of how these settings affect the crawler. But thankfully, you don't need to know -- we made a practical, methodical tool to help you figure it out!

## Optimizing Crawler Runtime

How do we evaluate how well a crawler is working? At a high level, there's a simple metric: **how many pages are we fetching per minute?** Using that as a rough measure of performance, we can try different combinations of knobs to see what works best.

We provide a handy-dandy tool that does exactly this. From your `mwmbl` directory, run `./deploy/crawler/benchmark.sh` and wait for the results. (If you're on a VM, you can run this script instead: `curl -fsSL https://raw.githubusercontent.com/mwmbl/mwmbl/main/deploy/crawler/benchmark.sh | sh`.)

The benchmarker tests process counts from 1 to 5 and thread counts of 10, 25, 50, and 100: up to 20 combinations of parameters. It spits out a `benchmark-results-fetches-<date>.csv` file with the results.

This process takes about 30-40 minutes *per combination*, so you might want to leave it running overnight and check the results later.

## Interpreting the Results

The benchmarker will tell you which combination was the `WINNER` (e.g., `3x10`). You can also open the benchmark `.csv` file and decide for yourself which configuration you want to use. For example, the CSV may contain something like:

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

After the timestamp, the columns show the number of workers and threads, the warmup and test durations in seconds, the number of fetches per minute, the status, and any error. The test duration of 1800 seconds is 30 minutes.

In the example above, one run with a configuration of `1x10` on this machine resulted in ~564 fetches per minute. A run with `5x100` resulted in ~916 fetches per minute. (That's 50 times as many threads, but less than double the output!)

In this case, we can see `5x10` with a score of ~1704 and `5x25` with a score of ~1708. I would personally choose `5x10` since its performance is almost the same with fewer threads.

In your case, maybe you don't *want* Mwmbl to crawl as aggressively as it can. Maybe you need to share your internet connection with a lot of other people, so you prefer fewer fetches per minute. That's OK. The choice is yours; use the data to decide.

Armed with your empirical results, open up your `.env` file and set `CRAWLER_WORKERS` and `CRAWL_THREADS` to your chosen values. For example, for `5x10`, set `CRAWLER_WORKERS=5` and `CRAWL_THREADS=10`. Restart the crawler with `docker compose up -d` and enjoy!

## Wait, What? Why is 5x100 Slower Than 5x10?

"Wait!" you might say. "Why would 5x100 be slower than 5x10?" The answer is "it's complicated." While you may have 500 vs. 50 threads (things running on your PC trying to fetch pages), it's possible that:

- Your computer runs out of network resources with `5x100`, so those 500 threads are fighting each other, whereas `5x10` might not have the same contention.
- The bottleneck on your PC might not be "how fast can I access websites?" but "how fast can I process data?"
- You might have been downloading a lot of data while the `5x100` configuration was running (one of the reasons we suggest running the benchmarks overnight).
- Your PC might run short of memory, CPU capacity, network bandwidth, or another shared resource.
- Any number of other things I can't fathom right now might be at play.

This is why the benchmarker uses an *empirical* methodology: it gives you actual, tested results.

## Conclusion

Mwmbl's crawler exposes a couple of nerd knobs, and the benchmarker gives you an empirical, systematic way to optimize its crawling performance. Using the data, you can decide how far you want to take the crawler and how much bandwidth you want to use.

If you have any questions, concerns, or comments, hit us up in our Matrix server (Crawling channel).
