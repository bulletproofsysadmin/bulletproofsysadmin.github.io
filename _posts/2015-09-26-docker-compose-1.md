---
author: bulletproofsysadmin
title: Docker-Compose Series - Part 1
featimg: 1.jpg
tags: [linux, docker, compose, centos, selinux, ci]
category: [devops]
---

This is part 1 in a 3 part series on Docker-compose.  A little more of the awesome that is Docker-compose will be shown off each week until we have completed the docker-compose basics.  We'll address a few of the questions that everyone has about compose but is too scared to ask.  
<br>
**Topics we'll cover**
<br>
- Installing compose and Basic Docker Principals - Part 1
- Using compose for container linking. - Part 2
- Creating something production-y with compose. - Part 3
<br>
**So you want to install docker compose?**
<br>
Installing compose is very simple.  Before we can install compose though we need to have a Docker host.  The best support for Docker and Docker with security is on RedHat.  Don't get me wrong Ubuntu's App Armor is great!  SELinux is more familar though and due to it's glacially moving nature RHEL7/Cent7 makes a great host for all your Docker containers.  Letting RHEL be good what it's at and any other flavors of Linux be good at what they're good at.
