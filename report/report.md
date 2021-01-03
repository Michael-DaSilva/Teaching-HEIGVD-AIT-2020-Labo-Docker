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

1. You probably noticed when we added `xz-utils`, we have to rebuild
   the whole image which took some time. What can we do to mitigate
   that? Take a look at the Docker documentation on
   [image layers](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/#images-and-layers).
   Tell us about the pros and cons to merge as much as possible of the
   command. In other words, compare:

  ```
  RUN command 1
  RUN command 2
  RUN command 3
  ```

  vs.

  ```
  RUN command 1 && command 2 && command 3
  ```

  There are also some articles about techniques to reduce the image
  size. Try to find them. They are talking about `squashing` or
  `flattening` images.



2. Propose a different approach to architecture our images to be able
   to reuse as much as possible what we have done. Your proposition
   should also try to avoid as much as possible repetitions between
   your images.

3. Provide the `/tmp/haproxy.cfg` file generated in the `ha` container
   after each step.  Place the output into the `logs` folder like you
   already did for the Docker logs in the previous tasks. Three files
   are expected.
   
   In addition, provide a log file containing the output of the 
   `docker ps` console and another file (per container) with
   `docker inspect <container>`. Four files are expected.

   In the folder logs/task4 you can find all file for this delivery.

   1_haproxy.cfg --> After docker-compose up --build  
   2_haproxy.cfg --> After running docker run -d --network heig --name s1 <imageName>  
   3_haproxy.cfg --> After running docker run -d --network heig --name s2 <imageName>  
   dockerps.txt  --> Information about docker ps  
   dockerInspectHa.txt --> Information about docker inspect ha  
   dockerInspectS1.txt --> Information about docker inspect s1  
   dockerInspectS2.txt --> Information about docker inspect s2  

   
4. Based on the three output files you have collected, what can you
   say about the way we generate it? What is the problem if any?

   We can see that we don't have an history about all container who in joined. Every time is rewrite all content.

### Task 5: Generate a new load balancer configuration when membership changes

### Task 6: Make the load balancer automatically reload the new configuration

### Difficulties