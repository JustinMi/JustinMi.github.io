---
layout: post
title: "Working with ROS"
date: 2017-05-21
---

This article is a quickstart tutorial on how to use [ROS](http://www.ros.org/), short for Robot Operating System. Since the creators can describe it much better than I can, to give a description I am just going to lift [a quote from the website](http://www.ros.org/about-ros/): "It is a collection of tools, libraries, and conventions that aim to simplify the task of creating complex and robust robot behavior across a wide variety of robotic platforms."

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

Then, we will install more modules using Python's `pip` package installer. If you don't have pip, install using these commands:

```shell
$ sudo -H python -m pip install -U pip  # Update pip
```

Then, install the following packages:

```shell
$ sudo -H python -m pip install -U wstool rosdep rosinstall rosinstall_generator rospkg catkin-pkg Distribute sphinx
```

If you get an error like `Cannot remove entries from nonexistent file /Users/anaconda3/lib/python3.4/site-packages/easy-install.pth` then you are facing [a well-known problem](https://github.com/ContinuumIO/anaconda-issues/issues/542) in `pip`, so add a `--ignore-installed` option to work around the error:

```shell
$ sudo -H python -m pip install -U wstool rosdep rosinstall rosinstall_generator rospkg catkin-pkg Distribute sphinx --ignore-installed
```

Then, we initialize `rosdep`

```shell
$ sudo -H rosdep init
$ rosdep update
```