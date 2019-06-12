---
title: "Rescheduling of Airflow DAGs"
thumbnailImagePosition: "left"
thumbnailImage: https://cdn-images-1.medium.com/max/1200/1*6jjSw8IqGbsPZp7L_43YyQ.png
coverImage: https://cdn-images-1.medium.com/max/2000/1*w4M-7X85wNNYx6WAFA-K5Q.png
metaAlignment: center
coverMeta: out
date: 2018-09-22
categories:
- programming
- data engineering
tags:
- airflow
- notes
---

This article briefly records the step to reschedule a DAG on Apache Airflow
<!--more-->

So this week one of my tasks is to reschedule some DAGs in our current data pipelines on Airflow. 

It all started with a complain made by my colleague, “Why is this query taking 30 minutes to complete, is there a DAG running?” 

You get my point, scheduling a DAG, particularly a massive DAG that often involves many crucial data transformation and transportation during the office hour is not something desirable. It happens to us not long ago, when one of us is testing a complex query and it ended up jamming both Airflow tasks and other manual executions.

As a data engineer, at some point of time you will need to be very familiar with the DAGs. They are like your babies, please take good care of them.

### Time Range

Let’s say we work from 9 am to 10 pm (because somebody likes work or run some queries at night) SGT, we will need to schedule DAGs outside of this period, which is 1 am to 2 pm UTC.

### Types of DAG

We are dealing with time, therefore it is important to identify first whether the DAG is temporal related - you kinda use the `execution_date` in your query / python jinja templating / or other stuff. 

You would probably like to keep the historical DAG run of this particular DAG as well, or perhaps you are using the `backfill` feature provided by Airflow, which is a great feature by the way. The following steps describe how to handle both of them.

### Steps 
##### Step 1. Identify scheduled time and actual execuion time of DAGs
It is important to actually go to airflow metadata database (the name …) to checkout the actual execution time of the DAG. Sometimes, for some unknown reason, your DAG does not start at the time designated. So to avoid further confusion, make sure check the actual execution time.

Now you need to decide whether you should reschedule a DAG based on the actual execution time.

If the actual execution time is acceptable, what you really need to do is just correct the start date, if not you will need to make some other changes.

##### Step 2.  Identify temporal relevant DAGs
Decide whether the DAG you want to move is dependent on time, as mentioned in previous section.

##### Step 3.1 If Not
- Rename (usually use a `_v*` suffix)
- Change Start Date
- Deploy and unpause your DAG
- If everything is great, delete your old DAG in metadata database (optional, somebody may find the disabled DAG disturbing)

##### Step 3.2 If unfortunately yes
- Change the last execution time in metadata DB to the desired starting time,  one `scheduled_interval` before.  (I might remember the wrong column name)
```
UPDATE dag_run SET execution_date = ...
WHERE dag_id = ...
```
- e.g. you have a daily DAG at 2am, now it is running every at 1am. So you change your latest DAG run to 1am.
- Change your start date

And that is it. You can relax and enjoy every successful slack notification when you get up in the morning, knowing everything is running fine at night.
