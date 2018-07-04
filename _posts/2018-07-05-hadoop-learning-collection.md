---
layout: post
title: 'Big Data Engineer Collection (in Progress)'
date: 2018-07-05
cover: 'https://s24255.pcdn.co/wp-content/uploads/2015/06/hadoop1.png'
tags: English Programming Data-Engineer Big-Data
---

A collection of Hadoop and Hadoop related projects learning note.

# Introduction

Being a data engineer, I am aware that the Hadoop stack is inevitable to me at some point, if I were to pursue a more
advanced career in this field. I have been contemplating this issue recently (because I have so much time), and I come
to the conclusion that there is indeed an increasing number of demand in this skill set.

Practical reason aside, as a person who love to learn new things, originally I have may be 50% of interest in learning
Hadoop. I have been putting this off for so long because for practical reasons, most startups do not need a "Big Data
Infrastructure" at first, not before they have decided what they truly want to do. Thus one good thing about stepping
outside of the professional world is that you could view all these from a more objective point of view and decide what
you are going to do with caution.

Anyway, I think it's time to learn it. And please be warned that the words in this post reflects my current state of
knowledge regarding Hadoop, it will get updated constantly, but it could contain errors.


# Background and Preliminary Research

Based on some of the introductory blog posts and official documentation of Hadoop, I think the core idea of Hadoop is
scalability and reliability. Of course these two words are used so frequently in many Software Engineer Courses, plus
what could better ensure these than a distributed system.

### Four Fundamental Modules

1. Hadoop Common - common utils supporting modules
2. Hadoop Distributed File System (HDFS) - for data access, seems like a building stone for distributed database to
3. Hadoop YARN - for job scheduling and resource management
4. Hadoop MapReduce - for processing of large data set which I guess this is what most people talked about because
this is the application level

### Other Apache Project built based on Hadoop

I think most people nowadays will automatically consider these as parts of the "Hadoop Ecosystem". Projects like Apache
Hive, Spark, Pig etc. Initially I thought all these projects are built based on top of Hadoop fundamental modules,
the later on I have seen comparing posts for both Hadoop MapReduce and Spark. Hadoop MapReduce itself looks like an
production-ready application and the other projects such as Spark, which seems to be 100 times faster than MR.


# Learning Path and Priority

### Stack

I do not think I should cover all technologies existing before I go bankrupt.

From priority high to low:
1. Hadoop - core of hadoop
2. Spark - for computation, ETL building
3. Hive + Cassandra + HBase - for data storage and retrieval

2 and 3 perhaps will be progressed simultaneously because of the nature of the project.

Others (if still can't secure a satisfied job with the three above):
1. Kafka - message queue
2. Ambari - Hadoop Web UI manager

### Books

1. Hadoop The Definitive Guide by Tom White
2. Learning Spark by Holden Karau, Andy Konwinski, Patrick Wendell & Matei Zaharia

I generally do not use books for studying new database.

So after setting up the framework, I should get started.


# Table of Contents



