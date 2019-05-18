# Images
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
docker image rm <image_name_or_id>
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

# Containers
**List all container**
```
docker ps -a  
```
**Stop a running container**
```
docker stop <container_name_or_id>
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

# Dockerfile
**Sample: php7 apache, map to port 80**
```
FROM php:7.0-apache
COPY src/ /var/www/html
EXPOSE 80
```
