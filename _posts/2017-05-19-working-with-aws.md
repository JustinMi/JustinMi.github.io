---
layout: post
title: "Working with Amazon AWS"
date: 2017-05-19
---

This blog post is just a dumping ground for my experiences working with Amazon AWS. I recently converted from Digital Ocean to AWS for my virtual hosting needs in an effort to learn this platform. I will document things I learn here and update it along the way.

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