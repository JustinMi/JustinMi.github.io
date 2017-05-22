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

# Step 1: Download and Set Up
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

# Step 2: Installation
We will first create a `catkin` workspace for our core ROS packages. 

```shell
$ mkdir ~/ros_catkin_ws
$ cd ~/ros_catkin_ws
```

Then we will use the `wstool` module that we installed earlier to install the 'desktop' variant of the core packages to install. 

```shell
$ rosinstall_generator desktop --rosdistro kinetic --deps --wet-only --tar > kinetic-desktop-wet.rosinstall
$ wstool init -j8 src kinetic-desktop-wet.rosinstall
```

If your installation fails or is interupted, enter

```shell
$ wstool update -j 4 -t src
```

Then we will recursively install all the dependencies for the `catkin` workspace:

```shell
$ rosdep install --from-paths src --ignore-src --rosdistro kinetic -y
```

The `--from-paths` option indicates that we want to install packages from `src`. The `--ignore-src` option indicates that we don't want to install any packages not found in `src`. The `--rosdistro` option is to indicate the type of distro we are planning to set up, in this case `kinetic`. If you have any errors, type:

```shell
rosdep install -ay --os=ubuntu:osx
```

And if your console returns

```shell
#All required rosdeps installed successfully
```

You should be fine. 

Next, we will build the `catkin` workspace. This might take a while due to troubleshooting many bugs in installation, so bear with me. Type in 

```shell
$ ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release
```




























