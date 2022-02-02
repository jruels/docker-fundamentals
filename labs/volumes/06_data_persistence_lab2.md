# LAB
## Data Persistence Part 2
*Lab Objectives*  
This lab demonstrates how to deploy the WordPress application using volumes to see how the data persists even if the containers are stopped or removed as long as the volume mounting point remains consistent. Students will also ssh into the MySQL database to validate that data is persistent across container builds.

Lab Structure - Overview
1.	Deploy WordPress Using Volumes
2.	Create a New User
3.	Investigate Persistence in Volumes

### 1. Deploy WordPress Using Volumes
Step by Step Guide
1.	Locate the IP address of the Master machine in lab folder.

2.	In a command line, enter  
`ssh -i </Users/…/>docker.pem root@<IP>`  
The .pem file will be provided by the instructor for this lab. This command will connect the console to the Docker machine.

3.	Create the Database container using a volume:  
`docker run -d --name db --volume mysql-db:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=password mysql:5.7`

4.	Inspect the mounts (Are these mounts bind-mounts or docker managed mounts?)  
{% raw %}
```
docker inspect -f {{.Mounts}} db
```
{% endraw %}
  
5.	List the Docker volumes:  
`docker volume ls`  
Output:  
    ```
    DRIVER              VOLUME NAME
    local               mysql-db
    ```
    Note the "mysql-db" volume name.

6.	Inspect the volume metadata of the mysql-db volume:  
`docker volume inspect mysql-db`  
Note the mount point.

7.	Deploy the WordPress container and link it to the container named db  
`docker run -d --name app -e WORDPRESS_DB_HOST=db:3306 --link db:mysql -p 80:80 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password s5atrain/wordpress-cli:latest`

8.	Gather the mapped port information:  
`docker port $(docker ps -lq)`  
Note the WordPress application container is bound to all interfaces on port 80.

9.	From a browser, open `http://<Master_IP>` and configure WordPress with the following credentials. Log in to WordPress after.  
`user: root`  
`password: root`  
`email: me@me.com`  

### 2. Create a New User
Step by Step Guide
1.	In the command line, docker-ssh into the MySQL database:  
`docker exec -i -t db bash`

2.	Enter these commands in the following order:  
`mysql -u wordpress -ppassword`  
`show databases;`  
`use wordpress;`  
`show tables;`  
`describe wp_users;`  
`select display_name from wp_users;`  
The CLI will display the following:  
    ```
    +--------------+
    | display_name |
    +--------------+
    | root         |
    +--------------+
    ```
Type `exit` twice to get back to Docker host
    
    `exit`  
    `exit`
   
    Note -ppassword is not a typo, and exit will need to be passed twice to fully exit bash. 

3.	Create a new user:  
`docker exec app wp user create jeff jeff@me.com --role='author' --user_pass=password --allow-root`

4.	ssh into the MySQL database and display the WordPress user list:  
`docker exec -i -t db bash`  
`mysql -u wordpress -ppassword`  
`use wordpress;`  
`select display_name from wp_users;`  
`exit`  
`exit`  

5.	Inspect the database mount point again:  
{% raw %}
```
docker inspect -f {{.Mounts}} db  
```
{% endraw %}

Note that it has not changed.

6.	Stop and delete the container, then validate that it has been deleted:  
`docker stop app && docker rm app`  
`docker ps -a`

7.	List the Docker volumes and inspect the mysql-db:  
`docker volume ls`  
`docker volume inspect mysql-db`  
Note that the mount point has not changed.

8.	Deploy the App container  
`docker run -d --name app -e WORDPRESS_DB_HOST=db:3306 --link db:mysql -p 80:80 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password s5atrain/wordpress-cli:latest`

9.	In a web browser, log in with the username and password for the new user you created earlier (jeff/password):  
`http://<Master_IP>`

10.	ssh into the MySQL database and display the WordPress user list to validate that the user is still local:  
`docker exec -i -t db bash`  
`mysql -u wordpress -ppassword`  
`use wordpress;`  
`select display_name from wp_users;`  
`exit`  
`exit`  

## 3. Investigate Persistence in Volumes
Step by Step Guide
1.	In a web browser, log in with the root username and password from earlier in the lab.  
`http://<Master_IP>`

2.	Click on Dashboard from the dropdown box at the top left of the screen that says wordpress.  

3.	Click on Pages and Add New. Add a simple page that will be recognizable in future steps. Note the permalink of the new page. Publish the page. Feel free to add additional content to this wordpress deployment. 

4.	Open the new page in the web browser and validate it works.

5.	In the command line, stop and remove the containers db and app and validate that they have been deleted (do NOT delete the volume, this is where the applications data lives):  
`docker stop db && docker rm db && docker stop app && docker rm app`  
`docker ps -a`

6.	Deploy the two containers again (this is the same command from the previous steps):  
    `docker run -d --name db --volume mysql-db:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=password mysql:5.7`  

    `docker run -d --name app -e WORDPRESS_DB_HOST=db:3306 --link db:mysql -p 80:80 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password s5atrain/wordpress-cli:latest`

7.	Open the published page in the web browser from step 3. Validate that it opens.  

8.	Destroy the environment and destroy the MySQL manage volume:  
`docker rm -f db && docker rm -f app`  
`docker volume rm mysql-db`

9.	Redeploy the containers one more time:  
    `docker run -d --name db --volume mysql-db:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=password mysql:5.7` 

    `docker run -d --name app -e WORDPRESS_DB_HOST=db:3306 --link db:mysql -p 80:80 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password s5atrain/wordpress-cli:latest`

10.	In the web browser, go to `http://<Master_IP>` to display the WordPress install page. Why does this now display the install page?  What is the source of the install page? 

11.	Now clean up the environment.  
`docker rm -f db && docker rm -f app`  
`docker volume rm mysql-db`

### Lab Complete!


<!-- 
LastTested: 2018-09-28
OS: Ubuntu 18.04
DockerVersion: 18.06.1-ce, build e68fc7a
-->
