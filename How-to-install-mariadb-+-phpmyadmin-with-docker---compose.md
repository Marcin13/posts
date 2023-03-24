---
author: "Marcin M"
title: "How to Install Mariadb + Phpmyadmin With Docker-Compose"
date: 2023-03-24T10:29:39Z
description: "The text explains how to use MariaDB and phpMyAdmin for web application management with Docker-Compose."
aliases: ["Docker", "Docker-Compose"]
draft: true
categories: ["Docker"]
tags: ["Docker"]
ShowToc: true
TocOpen: false
searchHidden: false
editPost:
  URL: "https://github.com/Marcin13/posts/blob/master/How-to-Install-Mariadb-+-Phpmyadmin-With-Docker---Compose.md"
---
## How to install mariadb + phpmyadmin with docker-compose
Running web applications, such as [Django](https://www.djangoproject.com/), requires the use of a database management system. 
While many [Django](https://www.djangoproject.com/) tutorials recommend using [PostgreSQL](https://www.postgresql.org/),
I have found that using the combination of [MariaDB](https://mariadb.com/) and [phpMyAdmin](https://www.phpmyadmin.net/) 
is very user-friendly for managing your database.
Although there are many tutorials available, some of them are outdated. Therefore, 
I have put together a quick guide to help you get started. Here are the prerequisites you need to meet before getting started:
You must have [Docker](https://www.docker.com/) and [Docker Compose](https://docs.docker.com/compose/) installed. You can find the documentation for Debian here and for Windows and macOS here. 
For **Docker Compose**, check out this [guide](https://docs.docker.com/compose/install/linux/).
To get started, create a **Docker Compose file** using this template (be sure to pay attention to the variables that need 
to be set, such as your password). Then, run **MariaDB** and **phpMyAdmin** using the file you just created.

## Create the docker-compose.yml file and run mariadb + phpmyadmin
The code is a **Docker Compose** file for creating and running **MariaDB** and **phpMyAdmin** services. 
It includes specifications for volume and network drivers, as well as environment variables for configuring **MariaDB**, 
including the root password and user credentials. The file also specifies ports for accessing 
both **MariaDB** and **phpMyAdmin**, and maps them to host ports.

```yaml
version: '3'

volumes:
  mariadb:
    driver: local

networks:
  db:
    driver: bridge

services:
  mariadb:
    image: mariadb:10.6
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: YOUR_ROOT_PASSWORD_HERE
      MYSQL_USER:  YOUR_MYSQL_USER_HERE
      MYSQL_PASSWORD: YOUR_USER_PW_HERE
    expose:
      - "40000"
    ports:
      - "40000:3306"
    volumes:
      - mariadb:/var/lib/mysql
    networks:
      db:

  phpmyadmin:
    image: phpmyadmin
    restart: always
    expose:
      - "40001"
    ports:
      - "40001:80"
    environment:
      - PMA_HOST=mariadb
      - PMA_PORT=3306
    networks:
      db:
```
after you save this file just run

```shell
sudo docker-compose up -d
```
I suggest you to run
```shell
sudo  docker-compose down -v
```
that should solve the issue.

## Conclusion:
The configuration in the _docker-compose.yml_ file sets up two services: MariaDB and PhpMyAdmin. **MariaDB** will run on port 
**40000**, and **PhpMyAdmin** will run on port **40001**. However, **by default**, these **ports** are only **accessible** from within the **Docker network**, not from your localhost.
To access PhpMyAdmin from your localhost, you need to create an SSH tunnel that forwards traffic from port 40001 on your 
localhost to port 40001 in the Docker network. You can do this by running the following command in a terminal window:
```shell
ssh USERNAME@YOURDOMAIN.COM -L40001:127.0.0.1:40001
```
Replace **USERNAME** and **YOURDOMAIN.COM** with your SSH username and domain name or IP address. This command will establish an
SSH connection to your remote server and create a tunnel that forwards traffic from port **40001** on your localhost to port
**40001** in the Docker network.
Once the tunnel is established, you can open your web browser and go to [http://localhost:40001](http://localhost:40001) to access PhpMyAdmin. 
You should be prompted with a login screen where you can enter the username and password that you set in the docker-compose.yml file.
Note that the SSH tunnel needs to be kept open for as long as you want to use PhpMyAdmin. 
If you close the terminal window or terminate the SSH connection, the tunnel will be closed, and you won't be able to 
access PhpMyAdmin until you create a new tunnel.



[//]:(![Screenshot.png]&#40;http://marcinmitruk.link/img/How-to-Install-Mariadb-+-Phpmyadmin-With-Docker---Compose/Screenshot1.png&#41;)






### Useful links:
https://david.dev/how-to-install-mariadb-phpmyadmin-with-docker-compose/

