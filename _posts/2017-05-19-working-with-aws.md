---
layout: post
title: "Working with Amazon AWS"
date: 2017-05-19
---

This blog post is just a dumping ground for my experiences working with Amazon AWS. I recently converted from Digital Ocean to AWS for my virtual hosting needs in an effort to learn this platform. I will document things I learn here and update it along the way.

### Prerequisites/Assumptions: 
* You have an AWS account
* You have an instance set up running Ubuntu 16.04

# Creating an Instance
To be filled later!

# Firewall--Security Groups

Found on the left hand side of the dashboard, Security Groups are Amazon's "firewall", which allow or disallow certain connections to come into the instances. 

<img src="{{ site.baseurl }}/images/securitygroup_sidebar.png" alt="Security Group Sidebar" style="height: 20em; margin: auto; display: block; vertical-align: middle;">

You must add or remove connections from the appropriate security group as well as change permissions on your instance (e.g. UFW if you are using an Ubuntu server) in order to have connections come through. For example, if you have a Django server running on port 8000, you must run 

```shell
$ sudo ufw allow 8000
```
as well as edit inbound rules in your security group by going to "Actions" > "Edit inbound rules" > "Add Rule", adding 8000 to port range, and setting source to be "anywhere". 

<img src="{{ site.baseurl }}/images/securitygroup.png" alt="Security Group">
<br>
<br>
<img src="{{ site.baseurl }}/images/inboundrules.png" alt="Inbound Rules">



# Ports
22--where SSH requests are fielded
80--where HTTP requests are fielded

# Sending Files to Server
In order to send files to your server, we will use SCP, which is short for Secure Copy, and is based off of the SSH protocol. Since we are already able to SSH to the server, we will be able to SCP files to the server in a similar manner. Type the following in, replacing the path to your .pem file, the directory or file name, your username, and the public DNS name of your instance.

```shell
scp -i /path/to/your/.pem -r directory_name username@your.public.DNS:destination/path
```

make sure you include the recursive `-r` tag after the path to your .pem file, or else you will get an error because bash will think `-r` is your path to the .pem file. 

