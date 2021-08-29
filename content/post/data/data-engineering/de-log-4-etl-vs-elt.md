---
title: "DE Log 4: ETL vs ELT"
thumbnailImagePosition: "left"
metaAlignment: center
thumbnailImage: https://panoply.io/uploads/versions/diagram4---x----750-328x---.jpg
coverImage: https://panoply.io/uploads/versions/diagram4---x----750-328x---.jpg
coverMeta: out
date: 2020-01-01
categories:
- data
- data-engineering
---

We have all heard of the term ETL. If you are working in the data field, you might have been asked to do some sort of ETL work regardless of your actual job description. 
<!--more-->

However, if we think it from a slightly different perspective, we could “invent” a different methodology in harvesting analytical data sets - we load raw / intermediate data into data warehouse first, and then transform it `in-place` in the data warehouse. 

I believe both approaches are useful and I would further argue that ELT approach is more advantageous under certain business contexts. In this blog post, I am going to first describe the fundamental difference between the aforementioned approaches, and then present three advantages that the ELT approach may possess over the other. 

# Overview

By examining and reducing the problem further, we could see that the fundamental difference between ETL and ELT is a result of the uneven distribution of provisioned computational (data transformation and aggregation) power. The traditional ETL approach emphasizes more on the transformations outside of the data warehouse, such as Spark jobs or even simple Python scripts using various libraries. ELT approach on the hand invests much more resources on the data warehouse itself, and transformations of data sets often occur within the data warehouse in the form of SQL scripts. Transformation that are not executable solely within a database engine will not be discussed here and should ideally followed a traditional ETL approach.

In my limited experience, I believe ELT tends to work better in a versatile, flexible and bootstrap environment, where a break from workflow is commonly acceptable to achieve the goals with minimum cost. While ETL works better in the more resourceful, stable business lines, where strict workflows are respected (for good reasons). 

We must also recognize that eventually as the business grows, more budget hence more computational power will be provisioned on both ends, and the problem that was originally choosing one of the two approaches will then inevitably evolve into: For any particular transformation job, where  should I put it? I do not want to dive too deep on the issue mentioned above because that deviates too much from the original discussion. Let’s come back and focus again on the choice between ETL and ELT, and here are my two (three) coins:

### ELT has a lower development cost, but the marginal gain is diminishing as the company grows

Transformation jobs tend to be just SQL scripts at the beginning. By relying the strong computational power within the data warehouse, data analysts or data engineers could easily implement the pipelines by testing complex SQL scripts directly against the pre-loaded raw data within data warehouse, provided that the employees know SQL, which is commonly required in the job description. This allows new startups to distribute data pipeline building workload within the entire data team. 

Besides development, debugging is also much easier as you could directly query against intermediate CTE/tables to check the validity of data. You could even automate data healthchecks simply using SQLs, again achievable by analysts which is great for smaller teams. 

As the data size grows and the analytical data layers in your data warehouse becomes deeper, one will have to shift the stable (less susceptible to changes) transformations and those cold (less frequently accessed) intermediate data sets outside of the data warehouse, thus slowly balance the distribution of transformation jobs and the usage of ETL/ELT approaches.

### ELT has a lower operational cost,  but it requires a rather big refactoring once things start to get too slow

Let’s be honest, which one is easier? To hire a decent SQL guy/girl or Python/Java guy/girl? Ok you may say that’s not a fair and meaningful comparison at all. Then how about the Ops work involved during coding deployments. At the initial phase of your data warehouse, an ELT approach could save a large amount of operational costs which could then translate to less salary expense and debugging hours. Ask the tech lead or the CTO to setup an auto-deployed SQL repo for your scheduler. Steps that a person worked on pipeline should only involved 

- Developing new SQL script (pipeline node)
- Testing SQL in DW
- Committed to repo and submit PR

Once the reviewer merges the PR, the pipeline should be automatically updated. Codes in this way is much more isolated (only in SQL) and again easier to involve people who are not originally trained in the software engineering field. 

However, once the size of intermediate data becomes too big to store in data warehouse, a big refactoring is needed to transform those SQL scripts into something similar to spark job and save them in S3. 

### ELT has a lower exploration cost

I guess this point is a relatively weak because it is of course easier to explore when you have less data and everything is querable within the same database. But imagine if we only load the final data sets required by internal reports / external data products into the DW, left those intermediate tables in S3, we won’t be able to easily check some random idea, validate some random hypothesis and do all kinds of experiments with just one SQL script. Sure I guess one could build a view and query against all Parquet files in S3, but LBH it’s not that realistic for smaller team  to setup this (other priorities always cut in the line). 

Again we need to acknowledge one thing, that the connenvience ELT provides comes with a cost, once intermediate data and pipelines are shifted outside of DW, the visibility and the accessibility of raw data would inevitably decrease, or in a well managed situation, not the visibility but mostly the accessibility. However, one could also argue that the visibility of such vast data sets may be overwhelming and not meaningful for everyone, a concrete point of which I would concede to. Personally I am trying to perfect the methodology that allows analytical data to be built layer by layer so that access rights will not be granted excessively. 

# Conclusion
I just came up with this post on the train, by knowing myself so well that most likely I would not revisit this article in the future even if my thoughts above has changed. What I can say is I have practiced the ELT approach (thanks to my Boss) for more than a year in a very small team with just one data engineer, I kinda of feel this is the reason why I am still able to get everything to flow even I am the only data engineer in the team. It works pretty nice with small teams, especially if you would like to save the money for your Spark cluster until you really need it. 

