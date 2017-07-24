---
title: Automation on Cloud
description: ETL
header: Automation on Cloud
---

When the number of scraping scripts I have reaches a threshold, 

- these scripts are burning my motherboard/battery/disk...
- I can't spawn more threads  because these scripts are eating all my RAM ...
- Can't do anything except watching the terminals/IDE/terminals in IDE...

Then I realise it is time to go 'cloud'. 

Simply put, I just need to rent another machine to help you run the script, so I can do whatever I want
on my own machine.

This article will talk about the initial setup of cloud automation from a beginner's point of view.

### Goal

I got a script scraping website A. The script was originally operating on my own computer, I want to
set up the script on a remote server, without GUI, probably at designated timing.

### Ingredients

Most of the remote servers would be Linux variants, but the commands mentioned later would be fairly standard,
which means you can use google to find it easily. So here I will just try to hide my ignorance in Linux with a bunch
of generic commands or English description.

Just FYI, the environment and tools I was using are:

Scripting language: Python 3 - honestly I don't really know how to use other scripting language
Tools: Selenium 3.4.3 + Firefox 54.0.1
Server: CentOS 7

Wanted to discuss something here:

1. Dynamic Loading
Many websites are not static, you can't just use a simple `urllib.request.urlopen()` to get everything you want.
selenium simulates the browser's behavior, e.g. login, input keys and loading javascript. There are more advanced 
way to load javascript, or even hijack the request so that you don't even have to touch the boring HTML, 
but I do know Javascript, probably I should ...

2. Headless

One of the thing we need to ensure is that we are comfortable (or at least know how with the aid of Google, yeah
that's me) to operate on a remote server without a GUI or Display. Ironically, sometimes we need a browser for
scraping, and it's taking tons of RAM. That is why we have to go headless. Originally, everybody would say hey go
try PhantomJS, so I tried, it works sometime, I do not have the time to dive deeper in PhantomJS right now, but 
I know it is powerful. Then I realise that PhantomJS is not the only way, there is a thing
called virtual display, so we could run the same Firefox setup on cloud, with just additional three lines of code.

```{python}
from xvfbwrapper import Xvfb
virtual_display = Xvfb()
virtual_display.start()
```


1. Choice of selenium + firefox

In fact there are a lot of combination
1. The new selenium + firefox stack would require a thing called `geckodriver`, which is not required when
Firefox is still at version 47. Simply specify it in the executable path:

```{python}
driver = webdriver.Firefox(executable_path='/path/to/your/geckodriver')
```

2. Choice