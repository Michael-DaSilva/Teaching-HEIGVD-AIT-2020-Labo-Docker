# Lab 04 - Docker

**Authors: Michaël da Silva, Nenad Rajic, Stefan Dejanovic**

### Table of content

[TOC]

### Task 0: Identify issues and install the tools

#### Install the tools

![](images/0_stats_page.png)

**URL of our repository:** https://github.com/Michael-DaSilva/Teaching-HEIGVD-AIT-2020-Labo-Docker

### Task 1: Add a process supervisor to run several processes

![](images/0_stats_page.png)

During this task we didn't get difficulties by following the instructions and they were clear and simple.

Regarding the use of a supervisor this one helps us to bypass the design's limit of Docker: **one process by container**. Because we need more than one process in the containers to manage correctly our infrastructure we need then this supervisor.

### Task 2: Add a tool to manage membership in the web server cluster

During this task we didn't have problems running the containers with Serf. But the current solution is not complete enough to work perfectly. 

If we start `ha` before the nodes the **Serf** service won't start until the nodes are up. And when the nodes are up but not `ha` they will return an error with Serf because they can't reach `ha`. We need a solution where `ha` starts first and then the nodes can connect to `ha` with Serf dynamically.

Concerning **Serf** and how it's working, the principle is simple: **Serf** uses what they call a **gossip protocol** that broadcasts messages to the cluster. Just like with humans and gossips the nodes in the cluster exchange informations randomly and these informations spread to the other nodes.

One of the other way TODO

### Task 3: React to membership changes



### Task 4: Use a template engine to easily generate configuration files

### Task 5: Generate a new load balancer configuration when membership changes

### Task 6: Make the load balancer automatically reload the new configuration

### Difficulties