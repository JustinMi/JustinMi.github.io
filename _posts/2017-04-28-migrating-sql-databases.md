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

-------------

# Step 1 (OS X): Downloading and Setting Up MySQL 
_You can skip this step if you already have MySQL set up on your computer._

First, we will download and set up MySQL using [instructions here.](https://dev.mysql.com/doc/refman/5.7/en/osx-installation.html) I used the [native package installer](https://dev.mysql.com/doc/refman/5.7/en/osx-installation-pkg.html) rather than the TAR. Once MySQL is installed, we will [set up a launch daemon that allows MySQL to start up on system/terminal launch](https://dev.mysql.com/doc/refman/5.7/en/osx-installation-launchd.html). _Make sure to save the temporary root password they give you!_

After you finish installing, double check that your server is running. On Mac OS, you can do this my going to System Preferences > MySQL.

-------------

# Step 1 (Ubuntu): Downloading and Setting Up MySQL 
_You can skip this step if you already have MySQL set up on your computer or server._

If you are using Ubuntu, you can install using 
```bash
$ sudo apt-get update
$ sudo apt-get install mysql-server
```

You will be prompted to set up a root password. Don't forget it, as you will use it later! Then, run the security script to configure MySQL.
```bash
$ sudo mysql_secure_installation
```

To test MySQL is working, enter
```bash
systemctl status mysql.service
```

And you should see an output like
```bash
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: en
   Active: active (running) since Wed 2016-11-23 21:21:25 UTC; 30min ago
 Main PID: 3754 (mysqld)
    Tasks: 28
   Memory: 142.3M
      CPU: 1.994s
   CGroup: /system.slice/mysql.service
           └─3754 /usr/sbin/mysqld
```

If MySQL isn't running, start it using 
```bash
sudo systemctl mysql start
```

-------------

# Step 2: Finshing Up Installation
_You can skip this step if you already have MySQL set up on your computer._

Try running `mysql` in terminal. You might get an error like "command not found". If so, in terminal, configure your computer's `$PATH` so it recognizes `mysql` as an exectutable:  
```bash
$ export PATH=$PATH:/path/to/your/mysql/bin
```  
For me, the command was was  
```bash
$ export PATH=$PATH:/usr/local/mysql/bin
```  
Check and make sure that your terminal recognizes `mysql` now. You might still get a MySQL specific error, but that's okay--we will fix that in the next steps. 


Unfortunately, this fix only works temporarily. If you open a new terminal tab, you will have to do `export PATH...` again. To allow `mysql` to be a recognizable command every time, we will edit your computer's bash profile. Open the bash profile using your preferred text editor:  
```bash
$ vim ~/.bash_profile
```  
In the file, copy-paste this:  
```bash
# Set architecture flags
export ARCHFLAGS="-arch x86_64"
# Ensure user-installed binaries take precedence  
export PATH=/usr/local/mysql/bin:$PATH  
# Load .bashrc if it exists  
test -f ~/.bashrc && source ~/.bashrc  
``` 
The above code allows `mysql` to be recognized every time. Save the file, restart terminal, and it should work.  

-------------

# Step 3: Creating MySQL Users
_NOTE: in this tutorial, we are altering the root user because it is assumed you do not have any other local users on your MySQL server. If you do, change the usernames accordingly_

Now we are ready to use MySQL! Enter
```bash
$ mysql -u root -p
```
Log in using your root password. If this is your first time logging in, use the password that you saved in step 1. Now, if you want to change the root password to something of your own preference, in the MySQL shell, enter
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_new_password';
```
Now, you have your root user set up and can log into it using your own password. 

If you want to create your own user, enter
```bash
mysql> CREATE USER 'your_new_username'@'localhost' IDENTIFIED BY 'new_password';
mysql> GRANT ALL PRIVILEGES ON * . * TO 'your_new_username'@'localhost';
mysql> FLUSH PRIVILEGES;
```
the `* . *` allows the user to have all access to the databases and tables in the server. For more fine-toothed assignment of priviledges, check out the [MySQL](https://dev.mysql.com/doc/refman/5.7/en/grant.html) documentation. 

-------------

# Step 4: Creating the Project Database
Then, to create the database for your project:
```
mysql> CREATE DATABASE your_project_name CHARACTER SET UTF8;
mysql> GRANT ALL PRIVILEGES ON your_project_name.* TO your_username@localhost;
mysql> FLUSH PRIVILEGES;
mysql> QUIT
```

## A note
If you want to skip typing `mysql -u root -p` every time you want to access your MySQL server, you can add a bash alias:
```bash
$ vim ~/.bash_profile
```
and copy-paste this:
```bash
alias mysql='mysql -u root -p'
```
Then, every time you want to access your MySQL server you just need to enter
```
$ mysql
```

-------------

# Step 5: Changing Django App Settings
Now, in your terminal, navigate to the root directory of your Django application. Run
```bash
$ python manage.py dumpdata > datadump.json
```
This will create a dumpfile of the data stored in your SQLite database. Then, install the relevent dependencies in your Python environment using `pip` or `conda`:
```bash
$ pip install MySQL-python
```
If you are using a virtual environment for Python, make sure you have `pip` installed in your virtual environment before using the `pip` commands above, or else the dependencies will be installed globally. 

`MySQL-python` is a self-contained driver that enables Python programs to interface with MySQL databases. `mysql-connector-python` is another database connector option, created and maintained by Oracle. The difference between the two is that `mysql-connector-python` is written in Python while `MySQL-python` is written in C. I suggest to use `MySQL-python` because it is faster with almost all SQL commands. The downside is that it is not compliant with Python 3. 

Finally, in your `settings.py` file in your app, change the `DATABASES` section to the following:
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
A reminder that `your_project_name` should be the same name as the database you created in MySQL in Step 2. `your_password` is the same password that you use to log into MySQL. The `HOST` and `PORT` values can be optional if you are simply hosting your webapp locally, but if you are running MySQL in a virtual server or a separate server then fill in your host address and port accordingly. 

## A Note
If you are using this MySQL server on your local machine, it is fine to store your MySQL root password in plaintext, but for a production-stage web app, we will need to add additional precautions to make sure your MySQL password is secure. In fact, even if you are using MySQL on your local machine, it doesn't hurt to take these precautions. We will set your password as an environmental variable, and access your password through the `os` module. 

To allow your own user account to have access to the password, we will edit our `.bashrc` file, which sets system-wide configurations. 
```bash
$ vim ~/.bashrc
```

At the bottom of the file, add
```bash
export mysql_pass="your_mysql_password"
```
This will set `mysql_pass` as an environment variable on your machine permanently.

If we want to let all users have access to the environmental variable, we will add a `*.sh` file to the `/etc/profile.d` folder of the production server. Alternatively, you can change `/etc/environment` but that is not recommended because it can only be accessed and changed by `root`, but `/etc/profile.d/*.sh` is equivalent to the `~/.profile` for each user. First, we will create the file for edit:
```bash
$ cd /etc/profile.d
$ vim name_of_your_shell_script.sh
```

In the file, we will add the same line as before:
```bash
export mysql_pass="your_mysql_password"
````

Either way, you will have access to the environmental variable. 

Then, at the top of `settings.py`, write
```python
import os
```
if it doesn't already exist. We will then change our database configuration to be

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_project_name',
        'USER': 'root',
        'PASSWORD': os.environ.get('mysql_pass'),
        'HOST': 'your_host_address', 
        'PORT': 'your_port',
    }
}
```
And you should be good!

**And as an aside, if you are working in production, you should apply the above process to your Django `SECRET_KEY` as well to remove security vulnerabilities.  


-------------

# Step 6: Make Migrations
We are in the home stretch! Now, all we need to do is apply any migrations you made to the new MySQL database. The details are all abstracted away for you, so all you need to do is run:
```bash
$ python manage.py makemigrations
$ python manage.py migrate --run-syncdb
```
Finally, when your MySQL database is all set up, load all the data you saved in the dumpfile in step 3:
```bash
$ python manage.py loaddata datadump.json
```

## A Note
If your migration fails, you may need to do some debugging. Every case may be different, so I cannot give much advice here, unfortunately. However, if you want to retry a migration after making some changes, you will need to start with an empty database. To do that, we will `DROP` the database and create a new one. To do so:
```bash
mysql> DROP DATABASE your_project_name;
mysql> CREATE DATABASE your_project_name CHARACTER SET UTF8;
mysql> GRANT ALL PRIVILEGES ON your_project_name.* TO your_username@localhost;
mysql> FLUSH PRIVILEGES;
mysql> QUIT
```
Then, you can try the migration again.



-------------

# Step 7: Wrap Up
And that's it! Now you have transitioned your webapp from SQLite to MySQL. While SQLite is quick, reliable, and useful for most development purposes, when your app transitions into production you often need to transition your backend database to fill the needs. And with this tutorial, you now know how!

If you are having trouble, here are some pages that I found helpful when working on this writeup: 


<a href="https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-16-04">https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-16-04</a>

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







