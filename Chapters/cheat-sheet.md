# คำสั่ง Docker เบื้องต้น

## [Images](https://docs.docker.com/glossary/?term=image)
An image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files.

**Pull an image**
```
docker pull <image_name>
```
**List all images**
```
docker images -a  
```
**Remove an image**
```
docker rmi <image_name_or_id>
```
**Create new image**
```
docker build -t <new_image_name> .
```
**Run an image**
```
docker run -p 80:80 <image_name>
```
```
docker run -p 80:80 --name <new_ps_name> <image_name>
```

## [Containers](https://docs.docker.com/glossary/?term=container)
A container is a runtime instance of an image--what the image becomes in memory when executed.

**List containers**
```
docker ps -a  
```
**Stop a running container**
```
docker stop <container_name_or_id>
```
**Start a stopped container**
```
docker start <container_name_or_id>
```
```
docker stop $(docker ps -a -q)
```
**Remove a container**
```
docker rm <container_name_or_id>
```
```
docker rm $(docker ps -a -q)
```

## System
**Show docker disk usage**
```
docker system df
```
**Remove unused data**
```
docker system prune -a
```

## [Dockerfile](https://docs.docker.com/glossary/?term=Dockerfile)
A Dockerfile is a text document that contains all the commands you would normally execute manually in order to build a Docker image.

**Sample: php7 apache, map to port 80**
```
FROM php:7.0-apache
COPY src/ /var/www/html
EXPOSE 80
```
> Dockerfile

## [Docker compose](https://docs.docker.com/glossary/?term=Compose)
Compose is a tool for defining and running complex applications with Docker. With Compose, you define a multi-container application in a single file, then spin your application up in a single command which does everything that needs to be done to get it running.

```
version: '3.1'

services:
  <name>:
    image: <image_name>
    
  database:
    image: redis
```
> docker-compose.yml

**Version**
```
docker-compose -v
```
**Validation**
```
docker-compose config
```
**Builds and attaches to container for service**
```
docker-compose -f stack.yml up
docker-compose up -d
docker-compose up -d --scale <name>=<number_of_instances>
```
