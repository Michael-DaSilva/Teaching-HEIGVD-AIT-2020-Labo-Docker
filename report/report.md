# Lab 04 - Docker

**Authors: Michaël da Silva, Nenad Rajic, Stefan Dejanovic**

### Table of content

[TOC]

### Task 0: Identify issues and install the tools

**[M1]** Do you think we can use the current solution for a production environment? What are the main problems when deploying it in a production environment?

**Answer M1:** No, we can't use this solution as seen for a production environment. There are several problems to fix before testing and deployment.

- Problem 1: There is only **two working nodes** and they are **hard-coded** in the files for the creation of the nodes. That's certainly not enough if we want to absorb and process the pick of traffic due to the holiday season. We need to **dynamically add and remove servers** according to the traffic and **without** **hard-coded configuration** (ex: **dynamic IP**).
- Problem 2: The bug that restart a container **needs to be fix** before to be in production. While this bug is already bad with a normal traffic, it could **seriously damage** the **brand image** of the website while in the holiday season, because of a **bigger traffic**, a **bigger number of potential clients** and so a **bigger number of failure** and **bad publicity** during the holiday season.



**[M2]** Describe what you need to do to add new `webapp` container to the infrastructure. Give the exact steps of what you have to do without modifying the way the things are done. Hint: You probably have to modify some configuration and script files in a Docker image.

**Answer M2:** To add a new `webapp` node to the infrastructure, we need to add the node into the config file of **HAProxy** (`haproxy.cfg`) in the `backend servers` section.  We do that into the **load balancer container**, we restart **HAProxy** and start the new node and it should be operational.



**[M3]** Based on your previous answers, you have detected some issues in the current solution. Now propose a better approach at a high level.

**Answer M3:** We should manage the addition of new nodes **dynamically** without **modifying the config file** each time by detecting the new nodes and add them in the cluster, and the same when we stop them / remove them.



**[M4]** You probably noticed that the list of web application nodes is hardcoded in the load balancer configuration. How can we manage the web app nodes in a more dynamic fashion?

**Answer M4:** The `webapp` node should **announce** their creation and availability to the load balancer and the load balancer should detect them **automatically**. Or we could perhaps configure a **pool of IP addresses** in the load balancer config and the nodes created would **choose an available IP** to connect to the load balancer.



**[M5]** In the physical or virtual machines of a typical infrastructure we tend to have not only one main process (like the web server or the load balancer) running, but a few additional processes on the side to perform management tasks.

For example to monitor the distributed system as a whole it is common to collect in one centralized place all the logs produced by the different machines. Therefore we need a process running on each machine that will forward the logs to the central place. (We could also imagine a central tool that reaches out to each machine to gather the logs. That's a push vs. pull problem.) It is quite common to see a push mechanism used for this kind of task.

Do you think our current solution is able to run additional management processes beside the main web server / load balancer process in a container? If no, what is missing / required to reach the goal? If yes, how to proceed to run for example a log forwarding process?

**Answer M5:** Our current solution doesn't let us to run multiple processes in the same container. We need a **supervisor** to do that.

We could maybe have a server in its own container that retrieve the logs from the web servers and we run a script in the `webapp` container in parallel that generate logs. 



**[M6]** In our current solution, although the load balancer configuration is changing dynamically, it doesn't follow dynamically the configuration of our distributed system when web servers are added or removed. If we take a closer look at the `run.sh` script, we see two calls to `sed` which will replace two lines in the `haproxy.cfg` configuration file just before we start `haproxy`. You clearly see that the configuration file has two lines and the script will replace these two lines.

What happens if we add more web server nodes? Do you think it is really dynamic? It's far away from being a dynamic configuration. Can you propose a solution to solve this?

**Answer M6:** It is not really dynamic because we need to **write manually the new nodes into the script** and restart the service to be applied. Like we explained at question **M4** one of the solution should be to let the nodes **announce them-self to the load balancer** each time they are created, like a **cluster**.



#### Install the tools

![](images/0_stats_page.png)

**URL of our repository:** https://github.com/Michael-DaSilva/Teaching-HEIGVD-AIT-2020-Labo-Docker

### Task 1: Add a process supervisor to run several processes

![](images/0_stats_page.png)

During this task we didn't get difficulties by following the instructions and they were clear and simple.

Regarding the use of a supervisor this one helps us to bypass the design's limit of Docker: **one process by container**. Because we need more than one process in the containers to manage correctly our infrastructure we need this supervisor.



### Task 2: Add a tool to manage membership in the web server cluster

For this task, the logs of `ha`, `s1` and `s2` are stored in the `logs` directory, into `task2`.

During this task we didn't have problems running the containers with Serf. But the current solution is not complete enough to work perfectly. 

If we start `ha` before the nodes the **Serf** service won't start until the nodes are up. And when the nodes are up but not `ha` they will return an error with Serf because they can't reach `ha`. We need a solution where `ha` starts first and then the nodes can connect to `ha` with Serf dynamically.

Concerning **Serf** and how it's working, the principle is simple: **Serf** uses what they call a **gossip protocol** that broadcasts messages to the cluster. Just like with **humans and gossips** the nodes in the cluster **exchange informations randomly** and these informations **spread to the other nodes**.

One of the other way would be to use what Docker provide to us: **label of the containers**. We could use a software/container that **read the label of a container** (service obtained with the Docker Engine) to know from which image it was created. If the containers have the label of `node` for example the load balancer could know, via a service, that a new `node` container is created or down.



### Task 3: React to membership changes

For this task, again the logs are available in the `logs` directory into `task3`.

![](images/3_ha_logs.png)

If we check the logs of `ha` all the installed services are running and it detects the two `webapp` nodes that joined the cluster.

![](images/3_s1_logs.png)

We can see the same informations in the logs of `s1` (nodes connected to the cluster) and the logs of the web server running.

![](images/3_s2_logs.png)

Finally, `s2` node has the same information that `s1`. If we kill one of the node, the load balancer detect it and spread the information to the others nodes.

![](images/3_ha_logs2.png)

![](images/3_s1_logs2.png)


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