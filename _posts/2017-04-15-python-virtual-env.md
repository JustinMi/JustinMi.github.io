---
layout: post
title: "Quick Tutorial in Python Virtual Environments"
date: 2017-04-15
---

# Intro

What version of Python do you normally use? If you are like me, that may be a bit of a complicated question to answer. I personally use Python 3 for my day-to-day use (scripting, writing quick pseudocode, tediously practicing interview questions, etc.), because my school's [intro CS class](http://cs61a.org/) was taught in it. However, when I was learning Python initially, using [MIT's edX course in computer programming](https://www.edx.org/course/introduction-computer-science-mitx-6-00-1x-10), I used Python 2 for the same reason--the course was taught using Python 2. And when I work on my project at Berkeley's Institute of Data Science, I again use Python 2. Even though Python's developers themselves say that ["Python 3 is the present and future of the language"](https://wiki.python.org/moin/Python2orPython3), a lot of projects and individual people just haven't transitioned. As a result it's sometimes hard to keep track of which version of Python I am supposed to use. 

However, with the work done on [Anaconda](https://www.continuum.io/downloads) by the good folks at Continuum, this issue turns trivial! On the surface, Anaconda provides a huge compendium of Python libaries, but it also gives you the ability to create Python virtual environments! With this, not only can you easily switch between Python 2 and Python 3 in your terminal whenever you switch projects, you can even create special environments with specific dependencies for each specific project you work on! This simplifies things a great deal. While things like `virtualenv` exist, Anaconda is superior in that it combines the easy library-installation perks of `pip` with the easy environment-switching perks of `virtualenv`, allowing you to seamlessly switch between environments, create new ones, and install dependendencies.

In this tutorial, we will go over the basics of using Anaconda's environment manager, Conda. 

# A Note
This tutorial only goes over the basics of Conda, enough for you to get up and running with your newly-aquired virtual-environment-jitsu skills. However, if you want to explore further, Conda's docs for any command can be accessed with the `--help` tag. For example,
```
conda list --help
```
tells you exactly what `conda list` is and how it works. 

