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

# Creating Environments
We will create two environments, so we can practice switching between virtual environments. To create our first environment, type
```
conda create --name your_environment_name python=2
```
This will create a virtual environment in Python 2 named `your_environment_name`. When Conda asks you
```
proceed ([y]/n)?
```
Hit `y`. Then, we will create our second environment. 
```
conda create --name second_environment_name python=3 babel
```
This time, we created an environment in Python 3, with `babel` installed. To explore more commands, type `conda create --help`.

# Listing Environments
To see what environments you have, type
```
conda env list
```
You will get a display of something like this:
```
# conda environments:
#
your_environment_name                    /Users/justinmi/anaconda3/envs/python2
second_environment_name                  /Users/justinmi/anaconda3/envs/python3
root                                  *  /Users/justinmi/anaconda3
```
This shows that I have 2 environments--one named `your_environment_name` that runs Python 2 and one named `second_environment_name` that runs Python 3. I am currently on my root environment, so there is a `*` next to the root environment directory. If I switch environments, the `*` will switch accordingly. 

# Switching Environments
Switching environments is the crux of what makes virtual environments so great. Let's switch to `your_environment_name` first. 
```
source activate your_environment_name
```
You will notice that after a short pause, your terminal prompt changes! Now, `your_environment_name` is prepended to the prompt in parenthesis! That is a subtle reminder that your current environment is `your_environment_name`. When you type `python` in your terminal, you will get the Python 2 interpreter. In addtion, if you check `conda env list`, you will notice that the `*` changes postion accordingly:
```
# conda environments:
#
your_environment_name                 *  /Users/justinmi/anaconda3/envs/python2
second_environment_name                  /Users/justinmi/anaconda3/envs/python3
root                                     /Users/justinmi/anaconda3
```

Okay, now let's deactivate the environment. Type 
```
source deactivate
```
Now the parenthesis disappears and you are back in the root environment. You can type `conda env list` again to make sure. To activate the other environment, we do the same thing:
```
source activate second_environment_name
```





