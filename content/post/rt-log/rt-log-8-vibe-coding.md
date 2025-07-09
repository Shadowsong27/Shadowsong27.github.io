---
title: "RT Log 8: 5 Vibe Coding techniques"
thumbnailImagePosition: "left"
thumbnailImage: "https://www.mygreatlearning.com/blog/wp-content/uploads/2025/04/what-is-vibe-coding-1024x683.webp"
metaAlignment: center
coverMeta: out
date: 2025-07-09
categories:
- rt-log
---

This article talks about several techniques I used when I code. It is not meant for direct adoption by readers because we might be writing different types of code. I was focusing mostly on Data Engineering and Software engineering (backend) code. If you were a data scientist, take this with a pinch of salt.

<!--more-->

Some context on my stacks:

1. Tool - Cline
2. Models - Gemini 2.5 Pro for Planning, Claude 3.7 for Act
3. MCP - `sequentialthinking` , `context7` , `mcp-clickhouse` because I have setup my homelab warehouse with clickhouse. There might be other choices out there but Clickhouse is a pretty popular open-sourced data warehouse
4. Custom instructions: Cline memory bank with modification to my usual tasks. e.g. to run dbt tasks, I have a dedicated section in the custom instructions to setup the dbt execution environment when opening up a new shell. 

```
# DBT Execution

When I am executing DBT commands, I will always prepare the new bash shell session by running:
1. `source .venv/bin/activate` to activate the virtualenv 
2. to cd into `/Users/shadowsong/Code/asp/asp-core/dbt` folder. This is my working directory for DBT commands. I will always execute DBT commands under this directory.. 
3. run `export DBT_TARGET=dev`  
4. run `export DBT_PROJECT_DIR="/Users/shadowsong/Code/asp/asp-core/dbt/asp"` 

Once finished all these, run `dbt debug` to ensure everything is set up correctly.

This will prepare me the environment for executing DBT commands correctly.
```

1. `.clinerules` to indicate several coding practices.

### #1: Be super Explicit in Custom instructions, and normal explicit in standard prompts

I think we kinda expect nowadays, the model to understand our vague instructions. However, when it comes to custom instructions, it’s better to be super explicit. Using the custom instruction for dbt above, I started with `cd into `asp` folder` which it might execute several times and end up in a completely different directory. In fact, it has become my habit (though probably unnecessary as models become more intelligent) when writing prompts to always tell the model where the file is and where do I want to change it as much as possible. I don't think it's always needed for the standard prompts. 

For standard prompts, I will always try to balance my laziness with explicit context, especially in reviewing of an update. 

Point form replies help shape my thoughts just as if I am writing my PR review, without the need to add **euphemism** providing reasons. 

e.g.

```bash
1. rename the classname to `xxx` (here you don't have to specify which class to rename from usually)
2. abstract the recursion logic into a new method called `_apply_something` within the same class
```

### #2: Think critically, do not happily accept everything

One thing we need to be clear is that AI is here to help us deliver, we are ultimately be held responsible for producing code that smells. So be sure to review every analysis that the AI is producing while you are waiting, it will help you discover potential issue with the current execution direction before it’s too late, and sometimes even new knowledge (but obviously please fact checks this with other sources). 

If sometimes the updates are too massive to review, you should instruct the tool / model to produce less updates per run (I am used to big PRs sadly).

Just go check some instagram or 小红书 while you are waiting is theoretically not good but if you are on a stressful day hey why not? Mental health matters.  

### #3: Avoid multiple tasks per task, unless super small

I notice the performance of the model (or at least Claude 3.7) will degrade drastically (and sometimes, start to duplicating response) if the context is too big. This may seem to be an obvious rule but sometimes we get too carried away while chatting happily with our AI friend and watching it spits out shinny garbage in our project.

### #4: Be active to shape how you want the model to behave

If you do not like the generic `try-except` code that AI friend is generating or the massive amount of parse-args where in fact you want it to use `click` or `typer`, state it and write it down in the rule files. You need to mindfully and proactively do this as if you are teaching a student. Model will not know this and this is again another important thing I notice during my usage. If I am being lazy, I will have the same crap response some time again in the future so when you notice it, do it right. 

### #5: Know exactly when to think, and when to delegate

The first four points are mostly related to providing context (”Context Engineering” is indeed an appropriate word). The last note I want to mention is more on how to keep us afloat in today’s vibe coding environment and grow. 

To me, it’s such a good time to increase my time of coding daily because AI helps me automate things I am not fond of, time consuming, but yet crucially important - unit tests, e2e tests, generating mock data, remembering syntax, algorithms that I studied 7 years ago. I used to spend a huge chunk of time to do all these out of professionalism, but they are definitely not my favourite code to write, but I have done it a few times manually, and I know it DOES NOT help too much in my personal growing anymore, I am only doing it out of necessity. Thus, I am knowingly delegate that portion of the code to AI. 

However, if you have never written any recursion in real work context (like parsing the struct schema in Spark), I would at least recommend you to do it once to understand it, and then delegate to AI. This is something I could not stress enough, AI is not a magical box, you will be able to determine its upper limit. There are certain things you should experience and grasp yourselves. 

From a different perspective, sometimes AI are just not smart enough yet to perfectly execute your requests. It might very well take 2 - 5 more carefully constructed prompts for it to understand the exact needs you want. In this case, please, do it manually if you can within 20s instead of chatting and instructing AI with it for 5 mins, focusing on your objective which is to deliver. I am sure people who spend millions poaching AI talents from each other could have done a better job in upskilling our AI friend than we did in those 5 mins. 

# Conclusion

These are not meant to be prescriptive but rather what worked for me. As AI continues to evolve (and probably at a faster rate than we can imagine), we need to evolve our approaches too. The balance between delegating and thinking is fluid - what works today might be obsolete tomorrow. But I think the essence stays the same - be mindful, be critical, and most importantly, focus on growing your skills while leveraging AI to handle the stuff you've mastered but don't particularly enjoy. After all, we're still the ones responsible for the code that goes into production, so let's make sure it doesn't smell too bad.