---
title: Proxy Pool
description: ETL
header: Proxy Pool
---

In a typical web crawling process, a proxy is required for various reasons. Replacing a hardcoded proxy connection
string is troublesome if your crawling process scales up to a certain extent. That is why we need a micro service
internally to help us manage these proxies we found online, a proxy pool.

Similar services could be found online, both free and commercial. Truly I am surprised to see a paid proxy managing
service, because usually when people build their own crawler they will just build their proxy pools as well. 

I am no way close to master in building a proxy pool, and I know there are a lot similar application could be found
on GitHub, but I just want to do it myself. You all know the saying, "Don't reinvent the wheels.", but the fastest 
way to understand something is to re-invent that wheel.


### Architecture

Design a good architecture is always the difficult part for a inexperienced programmer like me. Perhaps some time
later I will have to re-design / modifying major parts of my architecture, but got to start first.


- extractor for proxy website
- rotator for optimising proxies
- server that respond to API calls
- embedded database that temporarily store the proxies