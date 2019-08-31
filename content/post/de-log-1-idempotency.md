---
title: "DE Log 1: Idempotency"
thumbnailImagePosition: "left"
thumbnailImage: https://www.mistrasgroup.com/media/1540/steel-pipeline.jpg?center=0.4425,0.49&mode=crop&width=1500&height=600&rnd=131921218510000000
coverImage: https://www.mistrasgroup.com/media/1540/steel-pipeline.jpg?center=0.4425,0.49&mode=crop&width=1500&height=600&rnd=131921218510000000
metaAlignment: center
coverMeta: out
date: 2019-08-31
categories:
- programming
- data-engineering
---

> In linear algebra, an idempotent matrix is a matrix which, when multiplied by itself, yields itself.
><!--more-->

# Overview

Today I would like to talk about idempotency in Data Engineering.

I first learnt this word in Year 1 Uni, an intro Linear Algebra course (probably my best Maths module ever in the entire uni life). 

The term `idempotent matrix` means:

> In linear algebra, an idempotent matrix is a matrix which, when multiplied by itself, yields itself.

Well in data engineering, it most likely means no matter how many times you execute a ETL job, 
it will return you the same transformation result, provided the source data is unchanged.

I have known this concept and the benefits of it at the early stage of my career,
 but only recently (after working on the Data Warehouse migration to Redshift) I have start to appreciate its importance.

### Incremental vs Idempotent
Before describing the benefits, I would like to define the following two types of ETL jobs:

1. Incremental job, ETL jobs that require incremental changes to a table or data storage, e.g. `append` or `update` mode
2. Idempotent job, or I often call it refresh job for naming convention (easier to spell), it is something that I have explained above.

# Benefits
Basically, when you have an idempotent ETL job:

### Minimal maintenance
No backfill required, no need to worry about discontinuity in data during execution, 
and you know exactly what data you are transforming during re-execution. 

This is the most important thing, though it might take a longer time in the process 
compared to incremental task (not necessarily, and if it is too long it means you should look for 
an alternative solution, e.g. move your Python Operator to Spark), 
but the cost and trouble saved are much more crucial here. 

The cost here could refer to debugging time, data validation and patching time etc.

### Easier to debug 
It is easier to debug since everything can be refreshed as many times as you like, any time you want as 
long as dependencies are fulfilled. Normally data debugging is different from functionality debugging 
because the data is often dynamic, and that posts some challenges to even locate the bugs.

### Less temporal dependency
One of the common incremental jobs is the building of a time series table, e.g DAU MAU computation.
They often require certain level of data quality because they are used frequently in making business decisions.
 
However, sometimes we have data arrived later than the execution date (it could be a few days late), that 
requires you to recompute and update the data if you want a more accurate result or the portion of delayed data
is too significant.

One way to approach is to setup another pipeline to adjust the numbers after the first execution, or 
simply delayed the execution by a few days (which is fine because honestly you can't do anything since
you do not have the data in data warehouse yet).

An idempotent job would save you the trouble to maintain two separate pipelines, and the dependencies in between,
it makes sure every newly arrived data are considered during daily transformation.

### Less bug-prone
A bit similar to point 2. This means there will not be any confounding 
factors contribute to your problematic transformation result, leaving you only the transformation logic to debug.

Otherwise you might think ... hmmmm I remember two days ago some guy just manually updated 
a static table, when was it, let me ask him / check logs first (if you do not want to disturb others),
 and ahhhhhhhhhhh it’s right before the execution time that's why! I have wasted two hours of my life again ...

### Works better with modern Data Warehouse
We used to have a lot of update statements, getting only the latest data chunks, 
update the necessary columns when we were still using Postgres as our Data Warehouse. 
Over time the data quality starts to drop because of accidentally failed ETLs. 
It was apparently a trade off between performance and data quality. 

After we shift to Redshift, single `insert` or `update` start to become costly, 
I am forced to rethink and redesign many of our jobs and I am so happy that I eventually got rid of all the `update` statements.

# Final comment
I did not mean we should always do idempotent jobs 
(sometimes we will have to use incremental jobs, they are great too but they require much higher maintenance),
 we have deadlines, we have legacy codes, it all depends on priorities. If the data quality is slightly off,
  it might not have an impact at all. So think twice before actually start to refactoring pipelines.
  
The ultimate goal is of course, to walk into the office every morning 
without worrying about something / somebody is breaking the pipelines again, thank you very much.






