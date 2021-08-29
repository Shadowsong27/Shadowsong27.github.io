---
title: "DE Log 5: Thoughts on Analytical Tables"
thumbnailImagePosition: "left"
metaAlignment: center
thumbnailImage: http://www.jetbrains.com/datagrip/img/screenshots/query-console.png
coverImage: http://www.jetbrains.com/datagrip/img/screenshots/query-console.png
coverMeta: out
date: 2020-01-09
categories:
- data
- data-engineering
---

I have taken some Database design course back in university days, though I have skipped almost all of the lectures as I was also self-learning during my first internship on the same matter, in a much more practical manner. I did not even know the word `OLAP`  back then. However, I am not dismissing the importance of data modelling in data engineering. On the contrary, data modelling is one of the important skills if you want to be a data engineer.
<!--more-->

This article aims to address some common problems in the following types of column found in analytical tables

1. Metric Column

2. Filters / Segmentations Column

3. Time Column

# Analytical Table Types
The word `analytical` suggests that this article will not talk about ER diagram or transactional type, normalised data modelling. In fact, there are in my opinion two broad types of analytical tables in general.

- Snapshot

- Historical

### Snapshot Tables
Snapshot tables are refreshed during each pipeline runs, making them up to date with the raw data. Any changes in data made by users would safely be included into such tables without much implementation work.

### Historical Tables
Historical tables are rather nasty, but extremely important and common for monitoring and time dependent reporting. The word `historical` suggests that it preserves certain attributes of the raw data. 

For example, if you are producing a report for a client, and you have computed some important numbers. On the next reporting period, your user base grew a little  and that important number inevitably changed if you are using `Snapshot` type of table. Of course this might be ok and understandable for some clients, especially when they were train in engineering, but sometimes it’s just easier for everybody if we abstract the complex but unnecessary aspect of data preparation away from the valuable and respectful clients. 

They are happy, we are happy. Data engineers need to do work.

This is an example, of course there are use cases for this which I don’t really want to spend my brain power on, you get the point.

### Other types

There are more than just these two types of tables in a typical data warehouse. You could have

1. Lookup Tables - static tables that does not often get updated or updated manually. e.g. hard to extract data, categorical hierarchy

2. Exploration Tables - temp tables created by your dear friends and value colleagues

# Time related Columns
This is very straight forward, a date / timestamp column in the table, usually will be converted into the x-axis for times-series visualisations, or act as an attribute waiting for further aggregations. Not much to talk about but there are some few traps and tricks.

However, depending on the purpose of the columns, we should treat them differently. 

### Used as a metric - Localisation

It is convention that all timestamps stored in database, if not specified should be treated as UTC timezone. In fact, many of the columns in raw data would be often in the format of `BIGINT`, the epoch. Since time data comes in different format, we need to standardise for analytical use.

Also depending on the analytical usage, one should consider whether to include UTC timestamp or Localised timestamp.

For example, in the immediate downstream after the extraction of raw data, we would normally prepare some big analytical that does not contains much aggregation, but normally transformation and cleaning for columns. This table, which serves as a foundation for other analytical tables, should contain both time columns.

However, if you are building analytical tables for monitoring purpose, ideally you should use the UTC time and omit the other. This is because monitoring is often associated with your infrastructure, to identify when a bug might cause a drastic drop in MAU, you linked the UTC timestamp in the table and your system log time, or converted once more to YOUR (not users) local time and link with your git commits timestamp.

When building dashboards to answer a time dependent question, say when does people sleep normally, you should obviously use local time. This is important but often neglected during practice.

### Used as a label - Weekly and Monthly

Using time a label is too common that I would not go too deep in example. Here it is more about a convention in a company, there are people in the world view the start of the week as Sunday, where others as Monday. So I guess you should pick one and stick to it when creating weekly labels. Monday is more likely the default choice by database `date_trunc` functions.

As for monthly label, it makes more sense to show the first date of each month as on the chart you definitely do not want to see 30th Sept as your label. However, the interpretation of `2019-06-01` within the dashboard needs to be clarified, does it mean metrics generated based on data before `2019-06-01` or `2019-07-01`? 

# Filters / Segmentations
Filters / Segmentation columns such as countries and languages play a crucial role in making the table more explorable. The difficult part of this is to anticipate the filters that make sense to your business, very much similar to set `distkey` and `sortkey` in AWS Redshift - because if you want to change it later, it won’t be so easy. You will need to recreate the table by changing a few column selections and downstream tables if needed.

If the `recreation` is not yet explained clearly, think about an analytical tables that has three columns - (DATE | COUNTRY | DAU). This allows you to explore DAU numbers by country. Now your boss would like to see a distribution according to languages simultaneously , you will need to recreate the table by adding a new column, which is troublesome.

Note the phrase `simultaneously`, here we are actually talking about a combinational usage for filters, it has always been the case for me during work too. However, recently I start to wonder do we really need a combinational filters in some cases? Does the information `users who are active in French (FR) also uses language Chinese (zh) matter for your business, because without considering this edge cases that, in some business context insignificant, could save the engineers some work by transforming the table into something like - (DATE | FILTER NAME | FILTER VALUE | DAU) 

and do
 
```
SELECT
    date
    , dau
FROM table
WHERE filter_name = 'country'
    AND filter_value = 'US'
```
 
Less engineering work in the long run, easier to scale.

It’s really dependant on the business usage.

# Metric Column
This is your precious insight. By working every hard in gathering raw data, you finally have your processed / aggregated / transformed (whatever you would like to call) insights, or intermediates before the final insights. 

I would like to examine it from two different angles.

### Data Window size

I use the term data window because of `window functions`, frequently used in aggregating operations. 

### CASE WHEN size = 0
This is static data, simply inherited table by table, CTE by CTE into the final data set. However, exactly since they are static, you should consider abstract them into a lookup table if possible and logical.

### CASE WHEN size = all
This is computed base on the entire raw data sets. Not much to talk about to except if you want to be strict, truncate the head of your raw data during initial selection because Replication of Raw data does not happen all at UTC 0 o clock.

(You could argue that my extraction pipeline will make sure no trailing data is imported into DW, but lets just ignore tiny details ok?)

### CASE WHEN size = other time interval

You could also compute metrics based on sole on the previous complete 
day / week / month, for this you will need to specify the time range in the WHERE clause.

Personally, I prefer to use something like the following for daily. It’s more readable in my opinion, but other people use `INTERVAL` , `DATEDIFF` (which is an absolute value!) and others. Be careful with the inclusion and exclusion on each side of the time interval. Do not recommend `BETWEEN` because it’s both inclusion.

```
WHERE creationtime >= 'STRING_DATE'::DATE
	AND creationtime < 'STRING_DATE'::DATE + 1
```

For week and monthly, `date_trunc` or a variation of it is good enough.

### CASE WHEN you need window functions

There are quite a few window functions so I won’t try to categorise them and list them one by one (not really helpful). It’s just for these computations, it could be slow + memory intensive. So you might need to consider create temp tables if needed, and dropped them alter. 

### Maturity

I would like to talk about the maturity of the computed data. I define `maturity` as how close it is to be used in final presentation (BI dashboards). For example, we would like to compute some ratios, probably we are going to compute a denominator and a numerator separately then use a `/` to compute within the same SQL clause.

Yes you have finished your task, with filters, say `DAU / MAU` ratio in US.

How about now I ask you, can you tell me the global `DAU / MAU` ratio, and  how does it different from the one in US?

That’s when I realise I should probably not aggregated them to be fully mature. So instead of saving the ratio, I would like to choose to save `DAU` and `MAU` count separately, which are integers and can be further aggregated if I want a global count (using a `SUM()` in the next CTE).

This step could even be done in the Dashboard view level, depending on how power your data warehouse engine is. It is usually pretty fast, but if unfortunately that’s slow then I would say save both the ratio and intermediate columns in the table.

### Orientation

Finally, about how to save those columns. (This post is really long and I got very tired mentally at this point. ) 

There are two obvious way to save it:
1. Horizonally - each metric takes a column

Pros: intuitive, this make the read query pretty readable, easier to access more features within a query
Cons: less scalable as every additional feature requires a new column on the table 

```
SELECT
    metric_name
FROM table
WHERE …
```

2. Vertically - each metric is stored in row by row as a key-value pair, like a python dictionary

Pros: easier to scale
Cons: one extra WHERE clause, troublesome to computations among the metrics stored within a query, needs to use either CTE / Subquery or other operation to transform first. 

```
SELECT
    generic_value
FROM table
WHERE generic_name = ‘<METRIC_NAME>’
    AND …
```

In practice, I generally prefer the second approach as I believe writing separate CTEs is much much more easier than regenerate the table from an engineering’s point of view. However, if let’s say the metric requirement is really really static, then by all means use the first approach.

The above discussion only consider the usage of pure SQL. If we want to go one step further and include other scripting language, we could dynamically generate the the feature in a columnar format. That would be another post I guess.

# Conclusion

I don't want to conclude anything, see you next time.

