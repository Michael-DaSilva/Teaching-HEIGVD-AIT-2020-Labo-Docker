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

To reduce the image one of the best way to do that is to do like the second example:
```
  RUN command 1 && command 2 && command 3
```
Because it will reduce the number of layer. If we do it like the first example it will have 3 layers and with this one it will only have one layer.

For Docker squashing, the objectif is to flatten the image as much as possible. To do that it change all multiple layer in one layer to be smaller.

For Docker flattening, it will take all exisiting layers and export the as a single file system image (.tar). And then when we want to use it, we will import it but we must to be carefull because the history of container is not perserve. 

```
docker export # Flatten
docker import # Import when using
```

// TODO QUESTION 2

In the folder logs/task4 you can find all file for this delivery.

1_haproxy.cfg --> After docker-compose up --build  
2_haproxy.cfg --> After running docker run -d --network heig --name s1 <imageName>  
3_haproxy.cfg --> After running docker run -d --network heig --name s2 <imageName>  
dockerps.txt  --> Information about docker ps  
dockerInspectHa.txt --> Information about docker inspect ha  
dockerInspectS1.txt --> Information about docker inspect s1  
dockerInspectS2.txt --> Information about docker inspect s2  

If we compare the 3 file (1_haproxy-cfg -> 3_haproxy.cfg) we can see that we don't have an history about all container who in joined the cluster. Every time that a container is joined the cluster it rewrite the file haproxy.cfg. It could be better to have an history of all joined container.

**source:**   
https://docs.docker.com/develop/dev-best-practices/  
https://blog.codacy.com/five-ways-to-slim-your-docker-images/
https://forums.docker.com/t/how-to-flatten-an-image-with-127-parents/1600  
https://tuhrig.de/flatten-a-docker-container-or-image/

### Task 5: Generate a new load balancer configuration when membership changes

### Task 6: Make the load balancer automatically reload the new configuration

### Difficulties