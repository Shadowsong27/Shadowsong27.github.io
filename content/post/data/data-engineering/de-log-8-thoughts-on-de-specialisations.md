---
title: "DE Log 8: Thoughts on Data Engineering Specialisations"
thumbnailImagePosition: "left"
metaAlignment: center
thumbnailImage: https://www.offshore-technology.com/wp-content/uploads/sites/6/2019/12/shutterstock_570368662.jpg
coverImage: https://www.offshore-technology.com/wp-content/uploads/sites/6/2019/12/shutterstock_570368662.jpg
coverMeta: out
date: 2022-01-01
categories:
- data
- data-engineering
---

Happy New Year everyone! And I am too lazy to change the thumbnail image.

<!--more-->

As the title of data engineer starts to gain more recognitions, their responsible domains are  becoming more specialised. Data engineers have been asked to both cover from the lowest end of a pipeline, that is more infra involved - the DevOps side of fun when designing a data intensive application, to the highest end, that is more analytically involved - the data modelling and engineering support of your fellow business analysts. 

A person who is capable of developing a data product end-to-end, including the data pipelines that supports the data aspect of the product, which can be described by a prevalently used phrase these days - `full stack`. Yes besides `data alchemist` and `data magicians` on LinkedIn, we are getting `full stack` data engineers as well. 

Remember the core responsibility of a data engineer is to help your company / teammates have easy and reliable access to data, in vague terms, regardless what technologies you have decided adopt. Hence as the data engineering industry grows, the old craft of setting up your own distributed Airflow cluster, Spark Clusters, sadly becomes less important, all thanks to these commercially available services like Databricks and AWS MWAA. Unless you are developing some new customized in-house features, you do not and should not build everything from scratch. 

If you are like me, circulating from startups to startups in the last few years, most likely you have been working as this so called `full stack data engineer` . Some may argue that you are not learning anything in-depth with this behaviour, you are just Airflow monkeys busy fixing pipelines that breaks everyday. It tends to be true if you do not have a great mentor, but it could become a good learning package with proper guidance, and an effective way for you to nurture the `bigger picture`. Eventually if you want to dive deeper into the craft (pursuing the IC track or something), you will start to lean towards one of the two spectrums mentioned above.

There are different titles coined for the specialised data engineers. Categorising by tech stack, you will have `data enigineers` and `big data engineers` (this is a joke); categorising by functional roles, you will have a variety of `ETL engineers` `Data Warehouse Engineers (Chinese companies) / Analytical Engineers`, `Big Data Infrastructure Engineers` `(Big) Data Platform Engineers`. 

## Different Skillsets, Reusable Fundamentals

After all, these variants are still software engineers, that means they are supposed to solve problems using automated / programmatic ways. Although the skillsets and area of focuses involved could be drastically different for each title, there are a few fundamental skills that all variants of data engineers should possess (the discussion here is excluding common skill sets that should be a norm for all engineers: programming language, git etc…)

### Understand those generic data concepts

There are several data concepts that applies the entire life cycle of a data pipeline. If your variant of data engineers directly or indirectly works on a pipeline, being batch or streaming, you will most likely need to understand concepts such as Slowly Changing Dimension (SCD), Change Data Capture(CDC), ACID properties, data partitioning and those common challenges a distributed data system would face. By understanding these concepts in depth, you will then be able to make technical decision with confidence, avoid saying “I saw this on StackOverflow but I m not sure why” when ur pipeline boomed.

### Cultivate data quality sense

Data quality, data health, whatever, in my opinion is a set of dynamic, subjective metrics you derived to measure the quality of your data,. I can’t stress this more than enough, as long as data is flowing through your manned position, find a way to check it, it improves the general quality of life for everyone, especially yourself. Otherwise you will be running the same select count(*) statement over and over and over again.

Aside from the basic checks, you might need to derive some customized / business logic specific checks to monitor certain aspect of the data source. Average incoming data daily? Seasonal reporting deviation percentage? Now it’s time to apply some middle school mathematics (i hate math since young as a Chinese, but I do admit it’s tremendously useful).

### Understand a few various data tools

There are tons of tools and technologies out there for each component in the data tech stack. I would say one should have a firm grasp of at least one tech for each component, and preferably for both Hadoop and none Hadoop based solutions of applicable. For the complete list I would suggest to take a look at the awesome data engineering list here: https://github.com/igorbarinov/awesome-data-engineering

Now the why part, I would argue that some technologies in the same component are similar , hence the knowledge is transferable to some extend. If you understand Postgres, you should not have major problem understanding MySQL, but this barrier gets higher when the underlying implementation diverges. A file system based batch SQL engine like Presto could be more different than MySQL when comparing to Postgres. 

Let’s say you want to prepare a table with partitions. You know that partitioning the data would allow you to have better read performance in some cases, but not always. Despite the fact the different data warehouse / processing tools partition the data differently, it’s all about having a dictionary/index like access to shards of data, so the scanned data is less, read speed is faster. You would then realise you need to read how the new data tool implements this mechanism, after which you should be able to make a technical decision and get it with some hands-on.

### Stay informed with the latest data design trends and paradigms

The data engineering scene, like any other field in the software industry, is constantly changing. I still remember that in 2014 summer during my first university course, I was introduced with Hadoop & MapReduce, before I could even code in Python. Fast forward to today, Dataspark is gaining presence, cloud computing is a cliche and making products is easier than ever. It is utmost important to stay with the trend. What’s the latest open source/ proprietary technologies that makes existing solution obsolete? What’s the new tool that makes your original in-house design look like a student project? Constantly absorbing these knowledge, if not information, is helpful for you to have a bigger picture. 

## Start with Big Data or Small Data?

Short answer: it is better to have knowledge with Hadoop based tech experience and if you are a CS graduate, it’s going to help you land a decent job. However, if you are like me coming from a non-CS background, it really does not matter, you should start small and learn as much as you can on the job, so in this case the Small Data stack might be more suitable. 

Back then there is always this constant fear that I will not be able to catch up the latest technology, especially those Hadoop based, since I mostly operate in companies that data computational load and storage could be handled by a single Redshift cluster. And I stand by this design today, where the computation and storage are both handled by the data warehouse. This is the most economic solution for a small startup (well if I am really really familiar with ClickHouse I could probably bring down the cost even more I guess).

Now I am working in a company with Spark Hive and Presto, a lot of new info to digest at first but soon I realize many concepts are transferable, especially those that are closer to the fundamentals. 

There are after all several differences between a managed Redshift and a Hive+Spark setup, but ultimately one should understand both side of the things. It does not make sense to fire up a Spark cluster whenever.

## Whats next?

Keep reading blogs or other online materials. I like reading technical blogs because they are concise, they are really up-to-date and they are fun to read. It’s like as if a tech leader in the industry is chatting with you face to face, it’s much more fun than reading a book. 

Like this [https://www.dataengineeringpodcast.com/](https://www.dataengineeringpodcast.com/) 

That being said, I do need to gain knowledge more systematically sometimes, or dig deeper in the craft, that’s when I start to read a book. Shamelessly, I have never completely finished a technical book, I read it selectively and more like an information lookup. It has something to do with my learning habits - which is something based on needs (so I learn stuff that earns money, or brings joy)

On a concluding note, I will list a few things that I believe worth checking out. I am no master in anyone of the following fields, and I’m not going to be. A generalist seems to suit me better than a specialist so far. 

P.S. the jargons are extremely “useful” in interviews

### Concepts:

Snowflake vs Star Schema design

Data Warehouse paradigm shift: data warehouse → data lake → data lakehouse

Data layers in the data warehouse (and their technical names)

Data modeling 

Data Quality

Data Governance (Security, Access, Privacy and Compliance)

Pipeline & Query optimization 

### Books:

Read selectively as some of the concepts are outdated, but I do believe as the main contributors to the modern data warehouse, one should at least read them.

The Data Warehouse Toolkit - Ralph Kimball 

Building the Data Warehouse - Bill Inmon

Designing Data-Intensive Applications - Martin Kleppmann

Building the data lakehouse - Bill Inmon (Waiting to get this, I personnally still have not formed a good view on data lakehouse)

### And a few other things transferred from software engineering

Design patterns

Data Mesh

Domain Driven Design in Data Warehouse

The general testing mindset of software engineering and how it can be applied to data engineering