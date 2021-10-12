# LAB
## Capstone
*Lab Objectives*  
This lab is designed to test your knowledge of Docker gained from this course. By the end of the lab, you will have a working installation of Docker on your personal machine and a basic understanding of how to deploy applications locally, push and pull image repositories to Docker Hub, and build images using the methods described in the earlier labs.

Lab Structure - Overview
1.	Install Docker 
2.	Basic Docker Commands
3.	Deploy WordPress-as-a-Service
4.	Deploy WordPress Using Docker Compose
5.	Build a Dev Image Using a Dockerfile
6.	Optional Bonus Tasks
 
### 1. Install Docker Locally
1.	The first part of this lab is to install Docker on your local machine. Use the links provided for your OS and follow the instructions on the Docker website:

    [OSX/Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)  

    [Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)

    [Linux](https://docs.docker.com/install/)


2.	Before moving on to the next section, you should have a working installation of Docker.

### 2. Basic Docker Commands

1.	Push an Image from lab environment to your personal Docker Hub account.

2.	Pull an Image from your personal Docker Hub account to your local machine.

3.	Deploy cAdvisor locally.

4.	Deploy Portainer locally.

5.	Create a network bridge called Applications.

### 3. Deploy WordPress-as-a-Service

1.	The next task is to Deploy WPaaS for three customers with the following configuration:

    1.	Image: `<student_repository>/wordpress-cli:aio-manual`
    2.	expose port 80 (if required)
    3.	Dynamically assign mapped ports to port 80
    4.	Run on a network bridge called `wpaas_network`
    5.	Use a volume for `/var/html/www` to provide a level of data protection

### 4. Deploy WordPress Using Docker Compose
1.	Use the docker-compose command to deploy a WordPress environment with the following configuration:

    1.	Images:
        - `mysql:5.7`
        - `<student_repository>/wordpress:latest`
    2.	Volume: `./.data/db:/var/lib/mysql`
    3.	Set restart to `Always`
    4.	Define environment for db service as:
        - MYSQL_ROOT_PASSWORD: `wordpress`
        - MYSQL_DATABASE: `wordpress`
        - MYSQL_USER: `wordpress`
        - MYSQL_PASSWORD: `wordpress`
    5.	Define environment for app service as:
        - WORDPRESS_DB_HOST: `db:3306`
        - WORDPRESS_DB_PASSWORD: `wordpress`
    6. Set wordpress service to use dynamic host ports for 80 and 443

### 5. Build a Dev Image Using a Dockerfile
1.	Build a development image from ubuntu:18.04 using a Dockerfile with the following configuration (no specific order required for installing packages):

    1.	Install packages:  
    `mercurial`  
    `git`  
    `python`  
    `curl`  
    `vim`  
    `strace`  
    `diffstat`   
    `pkg-config`  
    `cmake`  
    `build-essential`  
    `tcpdump`  
    `screen`    
    2.	Create a user named "dev"
    3.	Create a home directory for user "dev"

### 6. Bonus tasks (optional)
1.	Add an additional WordPress host in your Docker Compose version using the same database, and place both web hosts behind an instance of HAProxy.

2.	Write a shell script that creates a two tier WordPress deployment:

### Lab Complete!

<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->
