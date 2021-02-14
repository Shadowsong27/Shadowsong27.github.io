---
title: "DE Log 3: Amazon Redshift disk space saving tips"
thumbnailImagePosition: "left"
metaAlignment: center
thumbnailImage: https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSA6b5Q1aWwI4R6xbMtiAH821mBwCWn1IVSxT2yjodNfeXvihNP&s
coverImage: https://www.blazeclan.com/wp-content/uploads/2013/08/Amazon-Redshift-%E2%80%93-11-Key-Points-to-Remember.png
coverMeta: out
date: 2019-11-04
categories:
- programming
- data-engineering
---


Recently Amazon Redshift launched a new console interface, which is pretty nice. It actually gives some valuable optimisation tips. A data warehouse is like a sword, you need to constantly sharpen it so it won’t lose its edge.
<!--more-->

I will cover a few disk space saving tips in this article. Though it is Redshift specific, I believe the idea could be generalised into other Data Warehouse solution.

To analyse the disk usage, you just need to run 
```
SELECT
    schema as table_schema,
    "table" as table_name,
    size as used_mb,
    tbl_rows,
    pct_used
FROM svv_table_info d
order by size desc;
```

It will give you a pretty nice overview of your disk usage. This is apparently the first thing you should do as it helps you pinpoint the priority of optimisation, and potentially the actual place of optimisation. Yes yes it could be more than just the storage layer, sometimes a complex analytical query would eat up all your storage too.

### Check the list of tables, and do some house cleaning

We create temp tables all the times. Some of them emerged and get committed as a pipeline step, some of them are just eating dust at the corner of the data warehouse. 

This tip is pretty straight forward, you just need to convince yourself that you really really do not need the table, and then drop it, just like what you do at house cleaning. Also, next time be a good guy and committed the queries somewhere, with documentation please.

### Identify heavy pipelines and optimise them

There are certain transformations that would temporarily take up a large chunk of disk space and only release it back once the process is done. It could be problem if you have several heavy pipelines running simultaneously.  It’s simple to identify them, just proceed to the Redshift console and look at the timeline for disk usage. If you see it rises to 100% usage periodically, checked the queries and optimised them by splitting the logic into several steps, persisted the transformation result of each step.

Note: Personally, I find this is also a pretty useful trick in avoiding some encrypted errors in Redshift. This happens all the time to my dear friends and value colleagues:

1. Wrote a query with 6 CTEs
2. Execute and gives an error message that is not even readable
3. Debug CTE by CTE and realise the bug does not make sense at all because the error throwing CTE is so simple and normal
4. Fixed it by persisted the result of the first few correct CTEs and execute the dead-simple-error-throwing CTE separately
5. Happily looking at the final result while cursing Redshift

### Incremental Processing

I have talked about idempotence in data engineering in one of my previous blog, however sometimes it is pretty costly and unnecessary to transform data in an idempotent fashion. Thus, incremental processing would be a nice alternative, it is essentially a tradeoff between performance and data quality. It is again a pretty straight forward idea, but one must be careful about the higher maintenance of such pipelines, and it often requires manual data patching when bad stuff happens. 

However, if your data is 
1. retrospective in nature, e.g. we don’t care about the past, like client reports which should stay the same even the past data has changed slightly due to various technical limitations
2.  Events (time series) data as it followed a strict non-reversible time line

I still recommend periodic auto data adjustment for the second type mentioned above, in order to counter the effect of delayed events / minor updates. e.g. a full replication every week.

### Hot/Cold data strategy + Spectrum / Athena query

Following the incremental processing strategy, we could also implement a hot/cold data pattern when we store the data. For large analytical events data generated from Google Analytics or Amazon Pinpoint alike, often we are generating user behaviour insights for a very recent period. 

E.g.
1. a post analysis of a feature release
2. an analysis to determine the next batches of optimisations

Thus, we only need to keep a fixed range of data, truncate the tail and append the new events everyday. It will act as a moving window, for the cold data, of course we will still store them somewhere like Amazon S3, and access them in ad-hoc way, using tools like Redshift Spectrum/Athena.

### Column Compression Encoding

This is something I find out recently, so I do have some very naive comparison stats.
The main idea is to compress your data by columns, period. You could google more on the algorithm and implementation, I have taken a look, it is a good read. 

To analyse:
```
ANALYZE COMPRESSION <table name>;
```

This will tell you how much you can compress by percentage for each column.

Before compression: 

- Total disk space usage: 9.14%

- Total time to generate: ~17mins

- Total time cost for the immediate downstream task: 1 m 14 s 513 ms

After compression:

- Total disk space usage: 2.xx% (I do not remember the actual value)

- Total time to generate: ~17mins (it’s pretty comparable)

- Total time cost for the immediate downstream task: 1 m 18 s 276 ms

So yeah definitely recommend this on large table, only unsurprising downside is you will need to rebuild your tables, as if you are not doing this when you are re-arranging your distkey and sort key

# Conclusion

Hope this helps.