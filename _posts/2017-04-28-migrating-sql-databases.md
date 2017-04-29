---
layout: post
title: "Migrating SQLite Databases to MySQL (Django)"
date: 2017-04-28
---

# Intro


SQLite is a great RDBMS that often comes "pre-installed" with many applications. It's main selling point is that the entire database is stored in a single file on the disk, making SQLite very fast and efficient, without losing any core functionality that many people come to expect from mainstream database management systems. Unfortunately, while SQLite's simple self-contained structure makes it great for the development and test phases of applications, it does not scale well when scaling to a multi-user application. There is no concept of user-management, where multiple clients need to access and use the same database. Changes are made through direct calls to the file holding the database. 

This is where MySQL comes in--it is the most popular large-scale database management system on the web, with lots of third-party support. It comes as a standalone database server, so applications talk to the MySQL daemon process to access the database itself, unlike SQLite which has a separate file corresponding to a database in each application. MySQL makes up for the shortcomings in SQLite by being feature-rich, scalable, and extremely powerfull, being able to handle _a lot_ of data at once. 

This tutorial will explain how to migrate a Django application running SQLite to MySQL, from the ground up. 

### Prerequisites/Assumptions: 
* You are using OS X
* Your webapp is using Django 1.10.x
* You have an existing database in SQLite

# Step 1: Downloading and Setting Up MySQL
_You can skip this step if you already have MySQL set up on your computer._


First, we will download and set up MySQL using [instructions here.](https://dev.mysql.com/doc/refman/5.7/en/osx-installation.html) I used the [native package installer](https://dev.mysql.com/doc/refman/5.7/en/osx-installation-pkg.html) rather than the TAR. Once MySQL is installed, we will [set up a launch daemon that allows MySQL to start up on system/terminal launch.](https://dev.mysql.com/doc/refman/5.7/en/osx-installation-launchd.html). _Make sure to save the temporary root password they give you!_


Try running `mysql` in terminal. You might get an error like "command not found". If so, in terminal, configure your computer's `$PATH` so it recognizes `mysql` as an exectutable:  
```shell
$ export PATH=$PATH:/path/to/your/mysql/bin
```  
For me, the command was was  
```shell
$ export PATH=$PATH:/usr/local/mysql/bin
```  
Check and make sure that your terminal recognizes `mysql` now. You might still get a MySQL specific error, but that's okay--we will fix that in the next steps. 


Unfortunately, this fix only works temporarily. If you open a new terminal tab, you will have to do `export PATH...` again. To allow `mysql` to be a recognizable command every time, we will edit your computer's bash profile. Open the bash profile:  
```shell
$ nano ~/.bash_profile
```  
In the file, copy-paste this:  
```shell
# Set architecture flags
export ARCHFLAGS="-arch x86_64"
# Ensure user-installed binaries take precedence  
export PATH=/usr/local/mysql/bin:$PATH  
# Load .bashrc if it exists  
test -f ~/.bashrc && source ~/.bashrc  
``` 
The above code allows `mysql` to be recognized every time. Save the file, restart terminal, and it should work.  


# Step 2: Creating MySQL Users
_NOTE: in this tutorial, we are altering the root user because it is assumed you do not have any other local users on your MySQL server. If you do, change the usernames accordingly_
Now we are ready to use MySQL! Enter
```shell
$ mysql -u root -p
```
Log in using your root password. If this is your first time logging in, use the password that you saved in step 1. In the MySQL shell, enter
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_new_password';
```
Then, to create the database for your project:
```
mysql> CREATE DATABASE your_project_name CHARACTER SET UTF8;
mysql> GRANT ALL PRIVILEGES ON your_project_name.* TO root@localhost;
mysql> FLUSH PRIVILEGES;
mysql> QUIT
```
If you want to skip typing `mysql -u root -p` every time you want to access your mysql server, you can add a bash alias:
```
$ nano ~/.bash_profile
```
and copy-paste this:
```
alias mysql='mysql -u root -p'
```

# Step 3: Changing Django App Settings
Now, in your terminal, navigate to the root directory of your Django application. Run
```shell
$ python manage.py dumpdata > datadump.json
```
This will create a dumpfile of the data stored in your SQLite database. Then, install the relevent dependencies in your Python environment using `pip` or `conda`:
```shell
$ pip install mysql-connector-python
$ pip install MySQL-python
```
or
```shell
$ conda install mysql-connector-python
$ conda install MySQL-python
```
Finally, in your `settings.py` file in your app, change the `DATABASES` section to match the following:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_project_name',
        'USER': 'root',
        'PASSWORD': 'your_password',
        'HOST': 'your_host_address', 
        'PORT': 'your_port',
    }
}
```
A reminder that `your_project_name` should be the same name as the database you created in MySQL in Step 2. `your_password` is the same password that you use to log into MySQL. The `HOST` and `PORT` can be empty if you are simply hosting your webapp locally, but if you are running MySQL in a virtual server then fill in your host address and port accordingly. 


# Step 4: Make Migrations
We are in the home stretch! Now, all we need to do is apply any migrations you made to the new MySQL database. The details are all abstracted away for you, so all you need to do is run:
```
$ python manage.py makemigrations
$ python manage.py migrate --run-syncdb
```
Finally, when your MySQL database is all set up, load all the data you saved in the dumpfile in step 3:
```
$ python manage.py loaddata datadump.json
```

# Step 5: Wrap Up
And that's it! Now you have transitioned your webapp from SQLite to MySQL. While SQLite is quick, reliable, and usefull for most development purposes, when your app transitions into production phase you often need to transition your backend database to fill the needs. And with this tutorial, you now know how!

If you are having trouble, here are some pages that I found helpful when working on this writeup:
<a href="http://stackoverflow.com/questions/3034910/whats-the-best-way-to-migrate-a-django-db-from-sqlite-to-mysql">http://stackoverflow.com/questions/3034910/whats-the-best-way-to-migrate-a-django-db-from-sqlite-to-mysql<a>

<a href="http://stackoverflow.com/questions/19189813/setting-django-up-to-use-mysql
">http://stackoverflow.com/questions/19189813/setting-django-up-to-use-mysql
</a>

<a href="https://docs.djangoproject.com/en/1.10/ref/databases/#mysql-notes
">https://docs.djangoproject.com/en/1.10/ref/databases/#mysql-notes
</a>

<a href="http://stackoverflow.com/questions/11546151/how-to-make-db-dumpfile-in-django
">http://stackoverflow.com/questions/11546151/how-to-make-db-dumpfile-in-django
</a>

<a href="https://www.digitalocean.com/community/tutorials/how-to-use-mysql-or-mariadb-with-your-django-application-on-ubuntu-14-04
">https://www.digitalocean.com/community/tutorials/how-to-use-mysql-or-mariadb-with-your-django-application-on-ubuntu-14-04
</a>

<a href="https://docs.djangoproject.com/en/1.11/topics/migrations/
">https://docs.djangoproject.com/en/1.11/topics/migrations/
</a>







