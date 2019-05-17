# Image
docker images -a  
docker image rm **<image_name_or_id>**

# Process
docker ps -a  
docker stop **<process_name_or_id>**  
docker rm **<process_name_or_id>**


# Create new image
docker build -t **<new_image_name>** .

# Run image
docker run -p 80:80 **<image_name>**  
docker run -p 80:80 --name **<new_ps_name>** **<image_name>**

docker stop $(docker ps -a -q)  
docker rm $(docker ps -a -q)

# Dockerfile
```
FROM php:7.0-apache
COPY src/ /var/www/html
EXPOSE 80
```
