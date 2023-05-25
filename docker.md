---
layout: page
title: "Docker"
permalink: /docker
nav_order: 3
---
# Materials
**Finished code for this lesson is available on the Instance Tool at the folder /home/ubuntu/01-docker/ .**
# Tasks

## Setup Validation
```
sudo docker version
```
![Docker Setup Validation](assets/images/docker/setup_validation.png)

## Run your 1st container
We will launch here a container that will rely on [Docker hello world image](https://hub.docker.com/_/hello-world) that just displays the Hello from Docker message.
```
sudo docker run hello-world
```
![Docker Setup Validation](assets/images/docker/hello-world.png)

## Expose your 1st container
We will deploy here a container based on [nginx web server official image](https://hub.docker.com/_/nginx)

```
sudo docker run --name nginx -p 80:80 -d nginx:latest
```
![Docker Nginx Validation](assets/images/docker/nginx_docker.png)

- -p option binds port 80 of the container to port 80 on the machine itself
- --name specifies a name for the container
- -d runs the container in background (detached mode)

You should be able to reach your nginx container using your instance public ip and your local browser.
![Container Exposition](assets/images/docker/expose_container.png)

## Display all containers

Using the docker ps command , you can show the containers that are running.
```
sudo docker ps
```
![Container Exposition](assets/images/docker/running_containers.png)

Adding the "-a" option, you can even see all containers (running/down)
```
sudo docker ps -a
```
![Container Exposition](assets/images/docker/all_containers.png)

## Create your 1st docker image
We will use the nginx official image we used before to create our own image that will show a different homepage.
1. Create a folder where your files will be located 
```
mkdir -p  01-docker/my-first-image
```
2. Create your Dockerfile
```
FROM nginx:latest
RUN echo "<h1>Scaleway Kubernetes Hands-on : Deploy your 1st workload</h1>" > /usr/share/nginx/html/index.html
```
3. Build your image
```
sudo docker build -t my-first-image 01-docker/my-first-image
```

![Build Image](assets/images/docker/build-image.png)

4. Run a Container upon your Image
```
sudo docker run -p 8081:80 -d my-first-image
```
You should be able to reach your nginx container using your instance public ip on port 8081 and your local browser.
![My 1st Container Exposition](assets/images/docker/expose_my1st_image.png)

## Deploy your image on a Container registry (Scaleway container registry)
Each attendee project has been provisioned with a Scaleway Container Registry. A container registry holds images that are built as part of your project. 
1. Retrieve your container Endpoint
![Container Exposition](assets/images/docker/registry_endpoint.png)
2. Retrieve the Scaleway Secret that have been using Scaleway IAM to provide access to your registry
3. Sign in to your container registry
```
sudo docker login $SCW_REGISTRY_ENDPOINT -u nologin --password-stdin <<< "$SCW_SECRET_KEY" 
```
- Replace $SCW_REGISTRY_ENDPOINT with the endpoint
- Replace the expression $SCW_SECRET_KEY with the secret
4. Build and Tag your custom image
```
sudo docker build -t $SCW_REGISTRY_ENDPOINT/my-first-image:0.1 01-docker/my-first-image
```
5. Push your custom image
```
sudo docker push $SCW_REGISTRY_ENDPOINT/my-first-image:0.1 
```
6. Run a container based on the pushed image
```
sudo docker run -p 8082:80 -d  $SCW_REGISTRY_ENDPOINT/my-first-image:0.1
```
You should be able to reach your nginx container using your instance public ip on port 8082 and your local browser.

# Reference Documentation
- [docker run documentation](https://docs.docker.com/engine/reference/commandline/run/)
- [docker build documentation](https://docs.docker.com/engine/reference/commandline/build/)
- [docker ps documentation](https://docs.docker.com/engine/reference/commandline/ps/)
