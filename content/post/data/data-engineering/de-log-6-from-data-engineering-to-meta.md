---
title: "DE Log 6: From Data Engineering to Meta Data Engineering - the future of Data Engineering"
thumbnailImagePosition: "left"
metaAlignment: center
thumbnailImage: https://images.ctfassets.net/k49d63tr8kcn/4jbw1fdWLuMUh0Ztlb2dOn/6964e6b873b904392990355ed48c983a/data_pipeline-1-.svg
coverImage: https://images.ctfassets.net/k49d63tr8kcn/4jbw1fdWLuMUh0Ztlb2dOn/6964e6b873b904392990355ed48c983a/data_pipeline-1-.svg
coverMeta: out
date: 2020-08-19
categories:
- data
- data-engineering
---

Data engineering jobs are really popular nowadays, mostly contributed by the rising demand of data insights and data driven decision making. 
<!--more-->

Data is essentially the go-to-crystal-ball these days whenever you want to check some random hypothesis just popped up in your mind while you are in the toilet. But it's more than that, people start to realise they need a systematic way to approach any data work. You cannot leave random SQL snippets scattered around in your local computer, not version controlled, and manually executing scripts every morning when you arrive in the office. It will be also too inefficient if you specifically hire some dedicated guys (SQL bois) running SQL in your legacy Microsoft SQL Server for the entire organisation. Some engineering work and engineering practices has to be installed here, that's how data engineering started, because people are sick of emailing Excel files around.

Today I want to talk about something more abstract, I call it `Meta Data Engineering` because it sounds cool. Data engineering in most companies now, especially who just hired their first data engineer team, are busy creating their pipelines, migrating data warehouses etc, which is the painful but necessary process when a company transitioned to apply the data engineering concept. However, after being in practice for a few years, I start to realise scheduling pipelines is boring and troublesome, the fear of which is one of the best qualities you can find in software engineers. If you have read my previous articles, you will know I like automation a lot. I would like to replace any manual repetition work in my life as much as possible.

Then you may be thinking, how can you automate the automation itself, when you have different pipelines and different type of tasks, and the evidently different underlying data? There are actually commonalities you can abstract, and so that it not only improves the efficiency and consistency of pipeline scheduling, but also hands over the very responsibility of pipeline scheduling towards the user - data analysts and data scientists, who should be the best person to decide what kind and what shape of datasets they would like to have.

That's basically meta data engineering, you are supposed to build frameworks and services to support the scheduling of tasks, the pipelines, the very popular nowadays `Data Platform` or `数据中台`, not the actual scheduling itself. I think this is the future of data engineering, because how hard can scheduling an Airflow task get? 

Speaking of the redistribution of responsibilities, I have thought about it briefly and here is the list that I think should stay with data engineers:

- infrastructure design, provisioning (yes unless you have a DevOps team)
- schema level design of data lake, data warehouse
- table level design for base tables, the ground layer of the data warehouse (here we need the participation of contextual knowledge, when normalisation occurs)
- services, frameworks or more complex and engineering demanding processing pipelines to interact with data, analyse data or train ML models more efficiently

Things that I think should be transferred to others (Data Analysts and Data Scientists)

- table level design for upper layer data warehouse
- scheduling of pure SQL transformation transformation (very dependent on use cases)
- design and installation of data validation
- POC, provision and deployment of machine learning models

For me personally I would like to have some tools that allow my teammates to build pipeline and schedule them in a GUI, like a back office, but unfortunately I am in and will probably always in a small team, so I might have to do it myself one day, or some bigger companies like Gojek could open source their framework.

Essentially, the responsibilities of data engineers will start to converge with software engineers, but serve a different purpose, another sign is the existence of data product managers nowadays. You need somebody who understands how data can be used to reinforce the decision making process in the company, in the form of modern technology. Eventually, as the world inevitably becomes more digitalised, data will be just as important as any product itself, or even more for those companies that sell data analytics as a service.

And for some time, a lot of data engineers, including me, will be responsible to write the pipelines that are not fun.

