---
layout: post
title: "Migrating SQLite Databases to MySQL (Django)"
date: 2017-04-28
---

## Intro

SQLite is a great RDBMS that often comes "pre-installed" with many applications. It's main selling point is that the entire database is stored in a single file on the disk, making SQLite very fast and efficient, without losing any core functionality that many people come to expect from mainstream database management systems. Unfortunately, while SQLite's simple self-contained structure makes it great for the development and test phases of applications, it does not scale well when scaling to a multi-user application. There is no concept of user-management, where multiple clients need to access and use the same database. Changes are made through direct calls to the file holding the database. 

This is where MySQL comes in--it is the most popular large-scale database management system on the web, with lots of third-party support. It comes as a standalone database server, so applications talk to the MySQL daemon process to access the database itself, unlike SQLite which has a separate file corresponding to a database in each application. MySQL makes up for the shortcomings in SQLite by being feature-rich, scalable, and extremely powerfull, being able to handle _a lot_ of data at once. 

This tutorial will explain how to migrate a Django application running SQLite to MySQL, from the ground up. 

## Prerequisites/Assumptions: 
* You are using OS X
* Your webapp is using Django 1.10.x
* You have an existing database in SQLite

## Step 1: Downloading and Setting Up MySQL
_You can skip this step if you already have MySQL set up on your computer._
1. Download and set up MySQL using [instructions here:](https://dev.mysql.com/doc/refman/5.7/en/osx-installation.html)
    1. I used the [native package installer](https://dev.mysql.com/doc/refman/5.7/en/osx-installation-pkg.html) rather than the TAR: 