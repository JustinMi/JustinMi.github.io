---
layout: post
title: "Working with ROS"
date: 2017-05-21
---

This article is a quickstart tutorial on how to use [ROS](http://www.ros.org/), short for Robot Operating System. Since the creators can describe it much better than I can, to give a description I am just going to lift a quote from the website: "It is a collection of tools, libraries, and conventions that aim to simplify the task of creating complex and robust robot behavior across a wide variety of robotic platforms."

This tutorial will get the average user up and running with ROS!
 
### Prerequisites/Assumptions: 
- You have Python 2.7 installed, and know how to code in it
- You are using OS X

# Step 1: Installation and Set Up
First, we need Homebrew to download and install most of our dependencies. If you don't already have Homebrew, go [here](https://brew.sh/) to set it up.

Then, we will install cmake:

```shell
$ brew update
$ brew install cmake
```

Then, we will install further dependencies:

```shell 
$ brew tap ros/deps
$ brew tap osrf/simulation   
$ brew tap homebrew/versions 
$ brew tap homebrew/science 
```

Those dependencies include Gazebo, a robot simulation package, SDFormat, an XML file that describes robotic environments, objects, and robots, and others. 

In addition, we will need to configure Python to work with the installed modules, as follows:

```shell
$ mkdir -p ~/Library/Python/2.7/lib/python/site-packages
$ echo "$(brew --prefix)/lib/python2.7/site-packages" >> ~/Library/Python/2.7/lib/python/site-packages/homebrew.pth
```

