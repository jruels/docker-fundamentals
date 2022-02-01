# LAB
## Networking Part 3
*Lab Objectives*  
This lab demonstrates how to use Docker link networks. First students will deploy a MySQL container and a WordPress container, and then they will link them.  

Lab Structure - Overview
1.	Build Network Links

### 1. Build Network Links
Step by Step Guide
1.	Locate the IP address of the Master machine in the lab folder.

2.	If on a Mac, or using Linux:  
In a command line, enter  
`ssh -i </Users/…/>docker.pem ubuntu@<IP>`
The .pem file will be provided by the instructor for this lab. This command will connect the console to the Docker machine.  

*If using Windows: Open Putty and connect to the session you saved earlier.*
 

3.	Run the following and deploy the MySQL container:  
`docker run -d --name db -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=password mysql:5.7`

4.	Deploy the WordPress container:  
`docker run -d --name app -e WORDPRESS_DB_HOST=db:3306 -p 80:80 -e WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_PASSWORD=password s5atrain/wordpress:latest`

5.	Wait a few seconds for container to start.  
Check the status of the app container:  
`docker ps -l`  
What do you think has happened to result in the container status you see? To confirm your hypothesis, check the container logs:  
`docker logs app`

6.	Remove the failed WordPress container. We will deploy it in an improved configuration in the next step.  
`docker stop app && docker rm app`

7.	Redeploy WordPress, this time with a link defined to the MySQL container:  
`docker run -d --name app -e WORDPRESS_DB_HOST=db:3306 --link db:mysql -p 80:80 -e WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_PASSWORD=password s5atrain/wordpress:latest`

8.	In the web browser, open http://`<docker_machine_IP>`:80 

This example now works because the MySQL Database and the WordPress application are able to communicate with each other. The appropriate firewall rules and the updates to /etc/hosts allows the two systems to communicate. 

9.	Clean up the lab environment:  
`docker rm -f app db`

### Lab Complete!

<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->
