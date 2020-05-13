---
title: "DA Log 1: A Quick Introduction to Data Analysis"
thumbnailImagePosition: "left"
thumbnailImage: https://tr2.cbsistatic.com/hub/i/r/2015/09/30/f66fef72-5d57-48cc-bd8c-7ec0941e73ef/resize/1200x/c7e3985615f96fd77c8a861f27b31a6d/dataanalysisistockrobuart.jpg
coverImage: https://www.import.io/wp-content/uploads/2019/09/data-analysis-blog.jpg
metaAlignment: center
coverMeta: out
date: 2020-05-13
categories:
- data analysis
tags:
- notes
---

This is a bold title I know, and I am not even a data analyst.
<!--more-->

I am a data engineer, but I do wield my analytical skills frequently to solve both technical problems (reading a monitoring chart) and analytical problems (explain why MAU drops).

So I think probably I should also write articles on analytics, just to make this skill less mysterious and keep refreshing my memory, of course, with my limited capabilities.

This article will cover many high level concepts in data analysis, instead of how to actually calculate the average of a column. In my opinion, the technical requirements in data analysis is extremely low, the difficult part is how to extract the correct, accurate information and present it in a understandable way.

Data analysis can be generally categorised to descriptive data analysis (like getting the average, 25th percentile) and inferential data analysis (like forecasting the trends of sales). In this article, I will be ONLY cover the descriptive type.

This article covers some common approaches to data analysis in the industry. I wrote this article because I feel data analytical skills will become an essential part of the skillset in future work forces. Product managers need to know how to read data, engineers in chemical plants need to know how to read charts too.

This is purely based on my experience both from the university courses and working experience. It might not be exhaustive. Feel free to comment and  add on if you think something are important too.

# Overview
Data analysis is actually quite intuitive if you apply your logic frequently. It is simply some logical conclusions yielded naturally, when applying a series of templated logic on the data sets. This might sound confusing, but let’s get to this later on. 

A few very very very important things before we actually start to talk about data or data analysis, and what does these actually mean in practice

### Correlation does not imply causation

The first part of this sentence is a very commonly heard phrase, it should act as a constant warning for you to keep doubting your conclusion until you think it is comprehensive and sound enough.

For (the famous) example, Amazon data shows as the number of diapers sale went up among the 30-ish male adults,  the number of beers also went up.  So the most direct conclusion you can draw is,  diaper sales will help with the beer sales, which is very weird. This trend does not really tell you what is the causation behind this correlation, and you certainly can’t blindly report to your boss that “hey I think they have a magic correlation and we should just trust this and make decision based on this”.  Well worst case scenario, if you have no other insights to offer and you can actually reproduce this trend in other similar user cohorts (e.g. same age, same gender but different region in the same country), this might be regionally generic and can be used as advise for decision making.

The main reason of this weird trend is 30-ish males feel can easily get bored when tending their babies, so they need beers.

### Data is never perfectly clean

Real world data can be extremely dirty, it contains noise that you will find both hilarious and annoying. You could have typo in string field, you could have 105 years old in `age` or you could have missing data too. After a while you will get very used to the existence of dirty data. 

This can be attributed to the poorly designed collection process, or it can be an embedded attribute in your data.  (How can you prevent people from entering their age as 105?)

However the handling is another big topic, as it depends on both the natural of your data and business context, and your analytical goal. For now let’s just remember that we need to do be aware of this, since this could lead disastrous conclusion.

E.g. Let’s say 70% users did not really fill in their gender in Asian countries like Japan, Korea, and you realise the majority of the users who have filled their gender status in these countries are females (90%), which severely deviated from the normal value (say 60%). If you do know notice the missing 70%,  you would wrongly conclude that most of the asian users are females, where in fact, its really just that Asian people tend to hide their gender status.

### Ask the right question

The analytical goal should never be static, even it is given by your boss. Yes your boss might have given you a specific question like “what is the gender distribution of all users in Singapore, in the age between 25 to 35”. What your boss was actually trying answer is: “what is the best tag line to use in order to target the consumers?” He is trying to segment the user base to yield the largest segment, so the tagline could hit the majority of the user. Then you as an analyst should be able to understand your boss’ actual needs and start to perform your data magic to attend to these needs.

Let’s say your product is a medium price lipstick,  you would definitely first try to cut out all males in your user, and then start to categorise the remaining females by other attributes, like age. However, if your product is a high end lipstick, you should consider including males who have strong purchasing power as it might be a very appropriate gift for their partners or friends. In this case, both products are similar in nature, but the strategies and the analytical goal is completely different. 

# Developing a Framework First
Like I mentioned before, analytical tasks are not just about writing SQLs and performing statistical calculations, in fact, they are just the last `implementation `step in the process.

When I am doing analytical tasks, what typically going on in my mind is the following:

### Shape of the Data
What is the dimension of the data, the shape of the data, which granularity is this data set at?

By knowing this, you will have a rough grasp of the data sets, and what potential meaningful metrics you can extract from this data sets eventually, especially after when you have performed many similar tasks.

If you are constantly dealing with events like data, meaning every record has a timestamp attached to it, the analytical metrics you can think of commonly related to time window. e.g. the number of unique occurrences within the first seven days of signup, or 30 days, depending on the nature of the product.

### Metrics Selection
What kind of metrics / aggregates are interesting to the business context / stakeholders? Average number of sales over one year is not really useful if your product is seasonal.

Here at this step you will need to select your metrics based on your understand of the domain, even the limitation of the data itself to provide meaningful support for decision making. 

### Conclusion validation

Is the conclusion sound enough? How can I verify it from other aspects?

This is like solving mathematic problems when we are in middle school, we solve the equation using the formula we memorised in the book. However we know there are more than one way to solve this question. After I finished the entire paper, I would use that to verify the answer.

Data analysis is the same, you will need to constantly verify your results even though they seem correct. A conversion rate of 5% seems normal because that’s probably the industrial average, but if your user retention is higher than the industrial average, meaning your user is pretty loyal, the conversion rate should probably more than 5% by intuition as the goodwill to pay is probably high too.

A lot of these are based on intuition and experience in the field, that’s why you will need constantly repeat this hypothesis testing cycle, until you are pretty convinced that the result / conclusion you draw from the metrics are sound.

### Data Visualisation
If I am showing the result to somebody who are not really familiar with data, how should I present / explain it?

This is technically not a data analysis step, but it is too important to be left unmentioned. And the reason is simple too: if you can’t get anyone to understand your results, or you can’t explain it in human words clearly, it’s either your results are wrong (overly complicated), or your results are wrong (just wrong).

---
So in fact none of these is involving any technical details, e.g how to write the SQL for certain type of aggregations or what chart to use to visualise the result. This is because whether it is SQL, Python or Excel, they are simply tools and when you are approaching a data problem, I would definitely recommend using a top-down approach, meaning you should get the overall analytical framework right. 

If I really have to give a weight to the skills, I would say technical skills determined whether you can perform the job, even if like a robot. So maybe 40%, but what truly makes you a good data analyst and differentiating yourself from others is the remaining 60%, where you need know how to ask the right question, and tell an understandable story.

# Conclusion
I think that’s enough said. I have not really talked about anything technical, meaning how to actual write a query, do a group by or anything like that. Here we are only discussing methodologies. I will write a more detailed article for how to actually
    how to actually execute your analysis with the previously mentioned framework if someone really wants to learn it in practice using either Python or SQL.





