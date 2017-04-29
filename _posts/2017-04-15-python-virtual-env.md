---
layout: post
title: "Quick Tutorial in Python Virtual Environments"
date: 2017-04-15
---

# Intro

What version of Python do you normally use? If you are like me, that may be a bit of a complicated question to answer. I personally use Python 3 for my day-to-day use (scripting, writing quick pseudocode, tediously practicing interview questions, etc.), because my school's [intro CS class](http://cs61a.org/) was taught using Python 3. However, when I was learning Python initially, using [MIT's edX course in computer programming](https://www.edx.org/course/introduction-computer-science-mitx-6-00-1x-10), I used Python 2 for a similar reason--the course was taught using Python 2. And when I work on my project at Berkeley's Institute of Data Science, I again use Python 2. Even though Python's developers themselves say that ["Python 3 is the present and future of the language"](https://wiki.python.org/moin/Python2orPython3), a lot of projects and individual people just haven't transitioned. As a result it's sometimes a nuisance to keep track of which version of Python I am supposed to use. 

However, with the work done on [Anaconda](https://www.continuum.io/downloads) by the good folks at Continuum, this issue turns trivial! On the surface, Anaconda provides a huge compendium of Python libaries, but it also gives you the ability to create Python virtual environments! With this, not only can you easily switch between Python 2 and Python 3 in your terminal whenever you switch projects, you can even create special environments with specific dependencies for each specific project you work on! This simplifies things a great deal. While things like `virtualenv` exist, Anaconda is superior in that it combines the easy library-installation perks of `pip` with the easy environment-switching perks of `virtualenv`, allowing you to seamlessly switch between environments, create new ones, and install dependendencies. It is a "one-command-fit-all" for managing environments and installing packages. For more details, you can see this [handy chart](https://conda.io/docs/_downloads/conda-pip-virtualenv-translator.html).

In this tutorial, we will go over the basics of using Anaconda's environment manager, Conda in terminal.

### Prerequisites/Assumptions: 
* You are using OS X
* You are familiar with the Unix terminal

-------------

# A Note
This tutorial only goes over the basics of Conda, enough for you to get up and running with your newly-aquired virtual-environment-jitsu skills. However, if you want to explore further, Conda's docs for any command can be accessed with the `--help` tag. For example,
```
$ conda list --help
```
tells you exactly what `conda list` is and how it works. 

-------------

# Installing Anaconda
_If you already have Anaconda installed, you can skip this part_  
Installing Anaconda is relatively painless. Just go to the [website](https://www.continuum.io/downloads) and follow the instructions to download and install.

-------------

# Creating Environments
We will create two environments, so we can practice switching between virtual environments. To create our first environment, type
```
$ conda create --name your_environment_name python=2
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

-------------

# Listing Environments
To see what environments you have, type
```
$ conda env list
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
$ source activate your_environment_name
```
You will notice that after a short pause, your terminal prompt changes! Now, `(your_environment_name)` is prepended to the prompt! That is a subtle reminder that your current environment is `your_environment_name`. When you type `python` in your terminal, you will get the Python 2 interpreter. In addtion, if you check `conda env list`, you will notice that the `*` changes postion accordingly:
```
# conda environments:
#
your_environment_name                 *  /Users/justinmi/anaconda3/envs/python2
second_environment_name                  /Users/justinmi/anaconda3/envs/python3
root                                     /Users/justinmi/anaconda3
```

Okay, now let's deactivate the environment. Type 
```
$ source deactivate
```
Now the parenthesis disappears and you are back in the root environment. You can type `conda env list` again to make sure. To activate the other environment, we do the same thing:
```
$ source activate second_environment_name
```
And if we type `python` in the terminal, we will see that now we have the Python 3 interpreter! We can now run programs in Python 3.

# Working with Packages
Let's say we want to install the `numpy` package. Let's search for it first, to make sure that it is in the Anaconda index. Type
```
conda search numpy
```
You will get a huge list of all the versions of `numpy` you can download. To download it, type
```
conda install numpy
```
This will install `numpy` on whatever environment you are currently on. To install on a different environment, type
```
conda install --name second_environment_name numpy
```
If you can't find a package through `conda search`, it is likely that Anaconda does not have access to that package. In that case, you can use the `pip` package installer, and it will still install to the current environment. 

To check what packages you currently have installed on your current environment, enter
```
conda list
```
Let's say we want to remove `numpy`. We will just enter
```
conda remove numpy
```

# Removing Virtual Environments
Let's say we don't need `your_environment_name` anymore. We will just type
```
conda remove --name your_environment_name --all
``` 
We can type `conda env list` to double-check.

# Wrap Up and Summary
And that's the long and short of it! Hopefully, now you can see why Conda's virtual environment manager is so powerful, and you can switch between projects in Python 2 and Python 3 like a pro! 


