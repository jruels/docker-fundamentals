# LAB
## Docker Compose
*Lab Objectives*  
This lab demonstrates how to use the Docker Compose feature. There are three examples that build towards an understanding of how to implement several containers using a docker-compose file to manage the deployment.  

Lab Structure - Overview
1.	Compose Hello-World
2.	Install WordPress
3.	Compose Full WordPress Deployment
 

### 1. Compose Hello-World
Step by Step Guide
1.	Locate the IP address of the Master machine in the lab folder.

2.	If on a Mac, or using Linux:  
In a command line, enter  
`ssh -i </Users/…/>docker.pem root@<IP>`
The .pem file will be provided by the instructor for this lab. This command will connect the console to the Docker machine.

*If using Windows: Open Putty and connect to the session you saved earlier.* 
 

3.	Log into the Docker host and run the following:  
Download the lab files   
`wget http://bit.ly/docker-compose-lab -O docker-compose.zip`  
Extract the lab files   
`sudo apt-get update && sudo apt-get install -y unzip`  
`unzip docker-compose.zip`   
List files in the compose directory  
`cd compose`  
`ls`  
The following items will be present:    
    ```
    example1  example2  example3
    ```

4.	Enter the example1 folder and list the contents. There will be a single docker-compose.yml file inside.  
`cd ./example1`  
`ls`

5.	Inspect the file docker-compose.yml:  
`vim docker-compose.yml`  
The file is populated as follows:  
    ```
    version: '2.2'

    services:

      hello-world:
        image: hello-world
    ```
    To exit vim, press the esc key and enter  
    `:q!`

6.	Use the docker-compose up command with a detach flag to run a hello-world container in the background.  
`docker-compose up -d`  
The output will show the hello-world container being created:  
    ```
    Creating network "example1_default" with the default driver
    Creating example1_hello-world_1...done
    ```

7.	Issue the next command to see the running containers.  
`docker ps`  
The hello-world container should not be listed. This is because it exits upon successfully running. It can be listed by passing the "all" flag.  
`docker ps -a`

  You can also use `docker-compose` to interact with containers that are defined in the compose YAML file.  
  List containers: `docker-compose ps`  

8.	The following command will show the logs that Docker recorded for the hello-world container.   
`docker-compose logs hello-world`

9.	Enter the following command to remove the container:  
`docker-compose down`

10.	Now, use docker-compose to re-run hello-world in the foreground. Compare the output you see with the logs that Docker recorded for the hello-world container.

11.	Use the docker-compose down command to clean up the environment

	
### 2. Install WordPress 
Step by Step Guide
1.	Navigate to the example2 directory and list the contents. There will be a single docker_compose.yml file inside.  
`cd ../example2`  
`ls` 

2.	This Docker compose file is designed to set up a small WordPress deployment.  
`docker-compose up -d`  
The output will show the WordPress container being created:  
    ```
    Creating network "example2_default" with the default driver
    Creating example2_wordpress_1
    ```

3.	Enter  
`docker-compose ps`  
Take note of the port that is listed for the WordPress container (8080).

4.	Navigate to the Master_IP.  
{% raw %}
`http://<Master_IP>:8080`
{% endraw %}
If you don't see the expected install page, review the status and the logs of the WordPress container. Is there an evident problem?  

5.	Back in the console window, enter  
`vim docker-compose.yml`
Delete the comment marks from in front of the MySQL container by moving the cursor down and using the `x` key to delete each `#` character. After deleting the comment marks, save and exit vim via `esc` `:wq`

6.	Enter  
`docker-compose up -d`  
The output will show the WordPress container starting up, and the new MySQL container being created.
    ```
    Starting example2_wordpress_1
    Creating example2_mysql_1
    ```

7.	After a few moments, the WordPress Installer will be available at {% raw %} `http://<Master_IP>:8080` {% endraw%}
in the web browser.

8.	Enter  
`docker-compose stop`  
This will only stop the containers from running, it will not remove them. Enter the following to validate they are still on the machine:  
`docker ps -a`

9. 	Use vim to edit the docker-compose.yml file so that WordPress is exposed on port 8082 of the host. After making your edits, restart the application in detached mode and confirm the change using a web browser. 

10.	List the available Docker networks, and identify the network being used by the WordPress application. Can you explain the derivation of the name of this network?

11.	Stop and remove the containers  
`docker-compose down`

12.	Recheck the networks in Docker. Is the application network still available?
	


### 3. Compose Full WordPress Deployment
Step by Step Guide
1.	Navigate to the example3 directory and list the contents. There will be a single docker_compose.yml file inside.  
`cd ../example3`  
`ls`

2.	Inspect the Docker Compose file.  
`vim docker-compose.yml`  
Take a few moments to answer the following questions to yourself:

    - Identify the volumes that are being created.
    - What is the driver for the mariadb data volume?
    - There are two services: mariadb and wordpress. Which one will be instantiated first?

    If you are having trouble answering these questions, ask the instructor for help.

3.	Exit vim.

4.	Enter  
`docker-compose up -d`  
This may take a few minutes to pull the full WordPress deployment.

5.	Verify that the containers are running:  
`docker-compose ps`

6.	Navigate to {% raw %}`http://<MASTER_IP>/wp-admin`{% endraw %} in the web browser.  

7.	Log in to WordPress with the following credentials:  

    `Username: user`  
    `Password: bitnami`  

8.	On the Dashboard page, under At a Glance, click on the Post link

9.	Edit the Hello World! post.

10.	Make any changes to the post on this page and click Update when finished.

11.	Navigate to {% raw %} `http://<MASTER_IP>` {% endraw %} in another web browser tab. The page that loads should be the WordPress blog with the updated post.

12.	Now, find the ID of the WordPress application container and make a note of it. Stop and remove the container. Confirm that the WordPress site is no longer available.

13.	List the available Docker volumes. Did removing the WordPress application container affect any volumes?

14.	Restore the application configuration using the docker-compose up command in detached mode. 

15.	Navigate back to the WordPress site in a web browser tab. Do you see the changes you made to the Hello World post in step 10? Why or why not?

16.	Back in the console window, stop and remove the application using docker-compose. Check the list of available Docker volumes. Has the list changed?

17.	Use the -v flag to docker-compose down to remove the volumes listed in the compose file:  
`docker-compose down -v`  
Recheck the Docker volume list after the command has run.

### Lab Complete!

<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->
