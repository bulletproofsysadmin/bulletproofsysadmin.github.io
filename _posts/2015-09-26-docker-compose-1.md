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
Installing compose is very simple.  Before we can install compose though we need to have a Docker host.  The best support for Docker and Docker with security is on RedHat.  Don't get me wrong Ubuntu's App Armor is great!  SELinux is more familar though and due to the glacially moving nature RHEL7/Cent7 makes a great host for all your Docker containers.  Letting RHEL be good what it's at and any other flavors of Linux be good at what they're good at.

<br>

So first let's get our Docker host running on CentOS 7.  This tutorial is based on a CentOS 7.1 minimal installation.  If you need the centos ISO they are best obtained from ["http://ftp.osuosl.org/pub/centos/7.1.1503/isos/"](http://ftp.osuosl.org/pub/centos/7.1.1503/isos/).  

<br>

After installing and patching the system:

Install the Extra Packages for Enterprise Linux **EPEL**.
{% highlight bash %}
    #yum install epel-release -y
{% endhighlight %}

<br>
**Next install Docker:**

{% highlight bash %}
#yum install docker -y
{% endhighlight %}
<br>
**Enable docker to start at boot time:**

{% highlight bash %}
#systemctl enable docker
#systemctl start docker
{% endhighlight %}
<br>

**From here we can install docker-compose:**

{% highlight bash %}
#yum install python-pip -y
#pip install docker-compose
{% endhighlight %}
<br>

Following the run of pip install docker-compose we can now execute docker compose commands.  Docker compose used to be known as fig.  So anytime you see compose named as "fig" they are interchangeable. Compose files are describe in .yml syntax or __yaml__.  Below is an example .yaml file for docker compose.

<br>

{% highlight bash %}
elasticsearch:
  build: elasticsearch/
  ports:
    - "9200:9200"
logstash:
  build: logstash/
  command: logstash -f /etc/logstash/conf.d/\*.conf
  volumes:
    - ./logstash/config:/etc/logstash/conf.d
  ports:
    - "5000:5000"
    - "9995:9995/udp"
  links:
    - elasticsearch
kibana:
  build: kibana/
  volumes:
    - ./kibana/config/kibana.yml:/opt/kibana/config/kibana.yml
  ports:
    - "5601:5601"
  links:
    - elasticsearch
{% endhighlight %}

<br>

This specific docker-compose file contains the following directories :
<br>
{% highlight bash %}
README.md		docker-compose.yml	kibana
elasticsearch		logstash
{% endhighlight %}

Each folder inside the compose parent directory contains a Dockerfile that describes how to build that Docker image.  In this case the design for the environment described in compose is a typical ELK stack.  ( Elasticsearch Logstash Kibana ) A set of tools for data analysis and dashboarding.  

<br>

**What value does compose provide over regular docker commands?**

You might be asking the question, "What does compose do that docker doesn't?".  Compose lends several advantages:

 - The ability to up/down the entire environment with a single line.
 - Automatic container linking.
 - Management of port maps.
 - Docker commands can still be used to inspect the state of running machines and interact.  ( After all it's only python )

<br>

**Basic Procedure for using a docker-compose file and containers.**

1. Find a compose repository.  
2. Clone it onto a directory on your docker host.
3. `cd` into that directory.
4. Run docker-compose up

> Note : That when you run docker-compose up without the -d flag what is printing out on screen is the output of STDOUT from all of your containers spinning up.  If you hit ctrl+c to cancel it will exit all the containers.  

**Check out the example container.**
<br>
[https://github.com/bulletproofsysadmin/docker-elk](https://github.com/bulletproofsysadmin/docker-elk)
<br>

In order to spin up this container we're going to follow the same process.
<br>
Clone the repository on the docker host.

{% highlight bash %}
    git clone https://github.com/bulletproofsysadmin/docker-elk
{% endhighlight %}

<br>

Change directory into the docker-elk folder and run docker-compose up

<br>

{% highlight bash %}
    docker-compose up
{% endhighlight %}

<br>

If you want the service to be externally accessible you'll need to open up the appropriate ports in your firewall.  Since this is CentOS 7 and you may not be famililar with firewalld I'll list those commands here as well.  

<br>

{% highlight bash %}
firewall-cmd --add-port 5000/tcp --permanent
firewall-cmd --add-port 5601/tcp --permanent
firewall-cmd --add-port 9200/tcp --permanent
firewall-cmd --reload
{% endhighlight %}

<br>

Validate that the containers are running by using `docker-compose ps`
{% highlight bash %}
# docker-compose ps
          Name                       Command           State     Ports           
-------------------------------------------------------------------------------
dockerelk_elasticsearch_1   /docker-entrypoint.sh       Up  0.0.0.0:9200->9200/tcp,   
                            elas ...                        9300/tcp                  
dockerelk_kibana_1          /docker-entrypoint.sh       Up  0.0.0.0:5601->5601/tcp    
                            /tmp ...                                                                          
dockerelk_logstash_1        /docker-entrypoint.sh       Up 0.0.0.0:5000->5000/tcp,   
                            logs ...                       0.0.0.0:9514->9514/udp,   
                                                           9995/tcp,                 
                                                           0.0.0.0:9995->9995/udp
{% endhighlight %}

<br>

Compose has a number of other commands that are of some use:  

{% highlight bash %}
Commands:
  build              Build or rebuild services
  help               Get help on a command
  kill               Kill containers
  logs               View output from containers
  port               Print the public port for a port binding
  ps                 List containers
  pull               Pulls service images
  restart            Restart services
  rm                 Remove stopped containers
  run                Run a one-off command
  scale              Set number of containers for a service
  start              Start services
  stop               Stop services
  up                 Create and start containers
  migrate-to-labels  Recreate containers to add labels
  version            Show the Docker-Compose version information
{% endhighlight %}

<br>

Many are the same as the docker commands.  The primary difference here is that when you use docker-compose you are operating on all the containers described in the .yaml file.


<br>

Now you know how to consume docker-compose configurations and start and stop them on a basic docker host.  In parts 2 and 3 of this series coming up we'll be looking at:

- How to create SELinux custom policy for docker containers.
- How to build your own docker images using compose.
- Data persistence using volumes.
- Starting and stopping containers with the host.
<br>
Thanks for checking the article out.  If you have any questions or feedback send to the@bulletproofsysadmin.com or tweet @bltpr00fadm1n.  
