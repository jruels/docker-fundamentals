#LAB
## Networking Part 2
*Lab Objectives*  
This lab demonstrates how to create and configure Docker bridge networks.  

Lab Structure - Overview
1.	Build a Bridge Network

### 1. Build a Bridged Network
Step by Step Guide
1.	Locate the IP address of the node-0 machine within the Ravello app.

2.	If on a Mac, or using Linux:  
In a command line, enter  
`ssh -i </Users/…/>DockerLab.pem ubuntu@<IP>`  
The .pem file will be provided by the instructor for this lab. This command will connect the console to the Docker machine.

*If using Windows: Open Putty and connect to the session you saved earlier.*
 

3.	List the current Docker networks  
`docker network ls`  
There following should be listed:  
    ```
    NETWORK ID          NAME                DRIVER
    bc712029c257        bridge              bridge
    ad32a3782fed        host                host
    e512120ca2ea        none                null
    ```

4.	View the metadata of the network bridge using ‘inspect’ on the network named bridge (Take note of the IPAM settings):  
`docker network inspect bridge`

5.	Review the metadata of the network called none  (Take note of the IPAM settings):  
`docker network inspect none`

6.	Review the metadata of the network called host (Take note of the IPAM settings):  
`docker network inspect host`

7.	Create a new network called wordpress using the --driver bridge:  
`docker network create --driver bridge wordpress`

8.	Validate that the network was created:  
`docker network ls`  
    Output:
    ```
    NETWORK ID          NAME                DRIVER
    bc712029c257        bridge              bridge
    36cc3de6c4aa        wordpress           bridge
    ad32a3782fed        host                host
    e512120ca2ea        none                null
    ```

9.	Review the metadata for the bridge network called wordpress:  
`docker network inspect wordpress`  
Note the IPAM section, the Gateway, and the container section.

10.	Create a new network named webapp:  
`docker network create webapp`

11.	Review the metadata for the bridge network called webapp:  
`docker network inspect webapp`

12.	Attempt to create a network that uses the host driver:  
`docker network create --driver host internet`  
    The machine will respond:  
    ```
    Error response from daemon: only one instance of "host" network is allowed
    Only one "host" network can exist; this is the network that provides access to the host network interfaces.
    ```

13.	Attempt to create a network that uses the null driver:  
`docker network create --driver null nowhere`  
    The machine will respond:
    ```
    Error response from daemon: only one instance of "null" network is allowed
    One one "null" can exist; this is the network that allows for the create of containers with only a loopback interface.
    ```

14.	Launch an instance of alpine:  
`docker run -d --net=wordpress alpine top`

15.	Inspect the wordpress network:  
`docker network inspect wordpress`  
Notice the addition of the container in the Containers section.

16.	Attempt to remove the wordpress network while the container is still running:  
`docker network rm wordpress`  
    The machine will respond with an error:  
    ```
    Error response from daemon: network wordpress has active endpoints
    ```

17.	Using the metadata output of the network wordpress, why does this network delete error?  
`docker network inspect wordpress`  
Remember the containers section.

18.	Using the metadata output of the network wordpress, find the container, and delete the container:  
``docker rm -f <container ID>``

19.	Using the metadata output, validate that the container is deleted:  
`docker network inspect wordpress`

20.	Remove the newly created networks:  
`docker network rm wordpress`  
`docker network rm webapp`

21.  Create a network using a specific subnet:  
`docker network create --driver=bridge --subnet=192.168.0.0/16 wordpress`

22. Create a network using the same subnet:  
    `docker network create --driver=bridge --subnet=192.168.0.0/16 webapp`  
    The machine will respond with an error:  
    ```
    Error response from daemon: cannot create network
    The error resulted from the docker daemon attempting to create a network with a subnet that already exists.
    ```
 
23.	Cleanup our demo networks and remove the wordpress network:  
`docker network rm wordpress`

24.	Create a network using the --driver bridge and name it br0:  
`docker network create --driver=bridge br0`

25.	Using the metadata output, review the IPAM config and note the subnet and gateway sections.  
`docker network inspect br0`

26.	Create a network defining a specific subnet:  
`docker network create --driver=bridge --subnet=172.29.0.0/16 br1`  

27.	Using the metadata output, review the IPAM config and note the subnet and gateway sections.  
`docker network inspect br1`

28.	Create another network defining the subnet and IP address range:  
`docker network create --driver=bridge --subnet=172.30.0.0/16 --ip-range=172.30.5.0/24 br2`

29.	Using the metadata output, review the IPAM config and note the subnet and gateway sections.  
`docker network inspect br2`

30.	Create a network defining a specific subnet, IP address range, and gateway:  
`docker network create --driver=bridge --subnet=172.31.0.0/16 --ip-range=172.31.5.0/24 --gateway=172.31.5.254 br3`

31.	Using the metadata output, review the IPAM config and note the subnet and gateway sections.  
`docker network inspect br3`

32.	Clean up the newly created networks  
`docker network rm br0 br1 br2 br3`

### Lab Complete!

<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->