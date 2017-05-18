---
layout: post
title: "Giving Local Network SSH Access to Your Wireless Machine"
date: 2017-05-18
---

Today I had the chance to connect a machine to my [lab's](http://autolab.berkeley.edu/) local network. I decided to create a short writeup of my experience, partly as a personal reference as I inevitably forget the details of how to do it.

### Prerequisites/Assumptions: 
* You are using Linux 
* You have an Asus router that you have administrative access to

-------------

# Step 1: Connecting to the Router Page

First, we will access the router admin page using 10.0.0.1 through our browser. Then, enter the username and password for your router when prompted. 

This page is the administrative settings page for your network. Most our work from here on will be done through here.

-------------

# Step 2: Setting Up Your LAN Connection

Here, we will first connect your machine to the LAN network. 

First, click on the LAN tab on the left hand side, then the DHCP Server tab on the top of the page. On the bottom of the page, add your computer to the list by filling out the boxes. 

The MAC address is found on your computer by typing `ifconfig` on your terminal. The IP address with be the next incremented number after the previous IP address on the list. For example if the previous IP address was 10.0.0.106, yours will be 10.0.0.107. The hostname is up to you--choose a consise and descriptive one, please.

Click the ‘+’ add button, and hit apply.

-------------

# Step 3: Setting Up Port Forwarding

Here, we will set up port forwarding, which will be the way other devices can SSH into your machine on your network. They will connect to the router, which will forward any requests to the appropriate port on your machine. 

Click on the WAN tab on the left hand side, then the Virtual Server/Port Forwarding. On the bottom of the page, add your computer to the list by filling out the boxes.

The Service Name should be consistent with the hostname you filled out before. The Port Range should be incremented by one from the previous highest port range number. For example, if the previous highest port range number is 8029, your port range should be 8030. The Local IP should be consistent with the IP address you filled out from the previous section. The Local Port is 22, where most machines listen to SSH. The protocol is TCP.

Click add, then click apply. 

_There is a chance that after you apply, the device does not show up on the list. This is a bug. Simply keep trying until you can get the device on the list._

-------------

# Step 4: Wrap Up

That should be it! If you try SSHing into your machine using `ssh [port range number] [user]@[IP address]`. If there are issues, please try out the troubleshooting methods provided below.

-------------

# Troubleshooting

In the terminal, type ‘sudo ifdown eth1’ and ‘sudo ifup eth1’ and enter your device password when requested. This should sync up your machine's IP address with the static one you provided.

And just in case, go to terminal and enter ‘sudo ufw allow 22’. This is allows the firewall to allow SSH requests.