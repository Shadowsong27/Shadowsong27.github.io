---
title: Multi-threads for MySQL
description: ETL
header: Multi-threads for MySQL
---
I have encountered the MySQL locking issue several times around a year ago, I merely
try-except it without understanding what actually caused the error. Guess right now
I have to be more serious about this problem so I did some digging just
now and I kind of understand what's going on.

Background:

I am running a script consists of several threads, each one of the threads
involved some ACID operations on MySQL database. Testing of individual thread
looks fine. However, when I started to run them concurrently, it will give me the
message like:

`Lock wait timeout exceeded; try restarting transaction`

Obviously I googled it and find out that it was caused because several threads
are using the same connection.

![This excellent](https://stackoverflow.com/questions/9173276/error-when-use-multithreading-and-mysqldb) SO post explains it.

So I guess the quick solution is:

open one connection for every thread
