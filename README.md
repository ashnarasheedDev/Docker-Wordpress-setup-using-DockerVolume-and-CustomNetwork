# Docker-Wordpress-setup-using-DockerVolume-and-CustomNetworks
The Docker WordPress setup using Docker volumes and custom networks involves deploying a WordPress application within Docker containers while leveraging Docker volumes for data persistence and creating custom networks for communication between containers. rdpress-setup-using-DockerVolume-and-CustomNetworks

Docker is an open-source platform that allows you to automate the deployment, scaling, and management of applications using containerization. Containers are lightweight, isolated environments that package everything needed to run an application, including the code, runtime, system tools, and libraries. Docker provides a consistent and reproducible environment for running applications across different operating systems and infrastructure.

**Here's a brief description of the setup:**

 -   Docker Volumes: Docker volumes are used to persist data in the WordPress application. By creating a Docker volume, you can store the WordPress files, plugins, themes, and uploads outside of the container. This ensures that the data remains intact even if the container is stopped, removed, or replaced. Docker volumes provide data persistence and separation from the container lifecycle.

 -   Custom Networks: Custom networks are created to enable communication between the WordPress container and other required containers, such as a database container. By creating a custom network, you can isolate the containers and control their communication. In the WordPress setup, a custom network allows the WordPress container to connect to the database container for storing and retrieving data.

  -  WordPress Container: The WordPress container is built using a Docker image that contains the WordPress application and its dependencies. The container is configured to use the Docker volume for persisting data. It is also connected to the custom network to enable communication with other containers.

  -  Database Container: Along with the WordPress container, a separate container is created for the database, such as MySQL or MariaDB. The database container is also connected to the custom network, allowing the WordPress container to access and store data in the database. The database container may use its own Docker volume for persistent storage of database files.


### Step 1 - Docker Installation

```
yum install docker -y
systemctl start docker.service
systemctl enable docker.service
```

### Step 2 - Creating Custom Network and Volumes

><b> Create Volume for mysql</b>
 ```
$ docker volume create mysql-vol
mysql-vol
 ```
 ><b> Create Volume for wordpress</b>
```
$ docker volume create wp-vol
wp-vol
 ```
> <b>Create a custom network</b>
```
$ docker network create wp-network
0a411e84f35356e68a51a2e2ad0c2fc9090c26bfb78d559ede0a877fd58acd82
``` 
### Creating Wordpress and MYSQL Containers 

><b>Create MYSQL Container</b>

  --name mysql-server: Assigns the name "mysql-server" to the container for easy reference.
 
  --network wp-network: Connects the container to the "wp-network" custom network.

  -v mysql-vol:/var/lib/mysql/: Creates and mounts a Docker volume named "mysql-vol" to the container's "/var/lib/mysql/" directory. This volume will be used to persist the MySQL database files.

  -e MYSQL_ROOT_PASSWORD="root@123": Sets the environment variable MYSQL_ROOT_PASSWORD to "root@123". This sets the root password for the MySQL server.

  -e MYSQL_DATABASE="wordpress": Sets the environment variable MYSQL_DATABASE to "wordpress". This creates a new MySQL database named "wordpress".
 
  -e MYSQL_USER="wpuser": Sets the environment variable MYSQL_USER to "wpuser". This creates a new MySQL user named "wpuser". 
 
  -e MYSQL_PASSWORD="wpuser@123": Sets the environment variable MYSQL_PASSWORD to "wpuser@123". This sets the password for the "wpuser" MySQL user.
 
   mysql:debian: Specifies the Docker image to use for the container. In this case, it uses the "mysql" image with the "debian" tag.

```
$ docker container run -d --restart always --name mysql-server --network wp-network  -v mysql-vol:/var/lib/mysql/ -e MYSQL_ROOT_PASSWORD="root@123" -e MYSQL_DATABASE="wordpress" -e MYSQL_USER="wpuser" -e MYSQL_PASSWORD="wpuser@123" mysql:debian

4c58f85361cd614108f78c9d98176d3f49a935851912ebfaa8f4ee3c0c770e41
 ```
 
 ><b> Create wordpress Container</b>

    -d: Runs the container in detached mode, meaning it runs in the background.
    
    --restart always: Configures the container to automatically restart if it stops or if the Docker daemon restarts.
    
    -p 80:80: Publishes port 80 of the container to port 80 of the host machine, allowing access to the WordPress site via the host's IP address.
    
    --name wordpress: Assigns the name "wordpress" to the container for easy reference.
    
    --network wp-network: Connects the container to the "wp-network" custom network.
    
    -v wp-vol:/var/www/html/: Creates and mounts a Docker volume named "wp-vol" to the container's "/var/www/html/" directory. This volume will be used to persist the WordPress site's files.
    
    -e WORDPRESS_DB_HOST="mysql-server": Sets the environment variable WORDPRESS_DB_HOST to "mysql-server". This specifies the hostname of the MySQL database server that WordPress should connect to.
    
    -e WORDPRESS_DB_USER="wpuser": Sets the environment variable WORDPRESS_DB_USER to "wpuser". This specifies the username for connecting to the MySQL database.
    
    -e WORDPRESS_DB_PASSWORD="wpuser@123": Sets the environment variable WORDPRESS_DB_PASSWORD to "wpuser@123". This specifies the password for the MySQL user.
    
    -e WORDPRESS_DB_NAME="wordpress": Sets the environment variable WORDPRESS_DB_NAME to "wordpress". This specifies the name of the MySQL database to use for WordPress.
    
    wordpress:latest: Specifies the Docker image to use for the container. In this case, it uses the latest version of the WordPress image.
 
 ```
$ docker container run -d --restart always -p 80:80 --name wordpress --network wp-network -v wp-vol:/var/www/html/  -e  WORDPRESS_DB_HOST="mysql-server" -e WORDPRESS_DB_USER="wpuser" -e WORDPRESS_DB_PASSWORD="wpuser@123" -e WORDPRESS_DB_NAME="wordpress"  wordpress:latest

Unable to find image 'wordpress:latest' locally
latest: Pulling from library/wordpress
f03b40093957: Already exists 
662d8f2fcdb9: Pull complete 
 ```
