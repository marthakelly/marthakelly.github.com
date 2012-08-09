---
layout: post
title: "Creating an EC2 instance with Fabric/Boto"
date: 2012-08-09 11:44
comments: true
categories: 
---

Lately i've been learning about networking/HTTP/various inner workings of the internet, so I've been working with AWS. I would like to share a script with you that I wrote to spin up EC2 instances.

### What is EC2?
[Amazon Elastic Compute Cloud](http://aws.amazon.com/ec2/) is a branch of AWS (Amazon Web Services) that allows you to rent virtual computers "in the cloud." For our purposes, we would like to configure our EC2 instance as a web server (but the set up for that will come in a later tutorial). *Pro tip:* If you're new to AWS you can use EC2 micro instances (a small amount of space, appropriate for personal applications) free for a year.

### What is Fabric?
[Fabric](http://docs.fabfile.org/en/1.4.3/index.html) is a Python library for automating application deployment and other systems administration tasks. Fabric allows you to easily run your Python programs through quality control at regular intervals (Continuous Integration). I use fabric to run tests, automate my git workflow, and deploy applications to local or remote servers.

The functions you want to run with the *fab* command go in a separate file called fabfile.py.

The script I've outline below is executed by running:

    fab create_server 

in your console.

### What is Boto?
Boto is a Python interface to the family of Amazon Web Services. In this article I'll only be referencing the Boto EC2 API (although, I highly recommend reading the [boto docs](http://boto.cloudhackers.com/en/latest/index.html)).

### Creating an Amazon EC2 instance with Fabric and Boto
First you'll need an [AWS account](http://aws.amazon.com/), your [AWS access key and AWS secret key](https://forums.aws.amazon.com/thread.jspa?threadID=49738aw), a [security group](http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/using-network-security.html) defined, and a [key_pair](http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/generating-a-keypair.html) generated.

{% gist 3305297 boto-fab %}

### What the what is going on here?
Let's go line by line!  
**line 1.** Import fabric.api  
**line 2.** Import yellow and green to use in the console (because I likes it)  
**line 3.** Import boto  
**line 4.** Import boto.ec2 API  
**line 5.** Import a local config file with my AWS credentials and other bits of information I would like to keep private  
**line 6.** Import the time module from the Python library (you'll see the usage of this in the script later)  
**line 8.** define the function that fabric will execute  
**line 12/13.** Print out to the console that this process has started  
**line 15.** create a connection object with a specific region we would like to connect  

** PRO TIP TO REDUCE MUCH PAIN **  

AWS products allow you to select [specific regional endpoints](http://docs.amazonwebservices.com/general/latest/gr/rande.html) to reduce latency (latency just means "slowness" in the response of the service)

By default (although this is not specified) Boto will use the eastern family of endpoints. If you would like to connect to the default you can use the 'connect_ec2' method: 

    conn = boto.connect_ec2(ec2_key, ec2_secret)
  
** END PRO TIP **  

**line 17.** Select the image you would like to use on the virtual machine you are renting. I chose Ubuntu. (Note this could contain a list of multiple images you want to use/iterate over, my list just has one item)

** More about AMI (Amazon Machine Images): ** Am image is just a snapshot of an Operating System (Windows, Linux, etc.) that you can put on a computer.

**line 19.** The run method returns a Reservation object which represents a collection of instances started at the same time. We're only starting one (for now). There are a number of arguments you can specify here, I'm specifying the min number of instances to start (1), max number of instances to start (1), the key_name I want to use (name of the keypair I generated), security group, and instance_type (t1.micro).

** More about Key Pairs: **

**line 22.** Get a reference to our specific instance object

**line 23.** Create a tag on this instance. This is optional. If you are managing many instances in one account, tags are a great way to organize and reference your instances.

**line 24.** While the image is being loaded on the virtual machine, update the user that this process is still running. Update the user every 10 seconds.

**line 29/30** When the image is done installing update the user and display the public DNS name of this instance.

Later you will use the public DNS name to ssh into the machine.

(( coming up soon, i'll write a boto/fab script to install the software necessary on this remote instance to run a django app  ))