# Docker Cheat Sheet

This is a "quick and dirty" cheat sheet of Docker commands to create, run, and remove images and containers..

To build a Docker image:

`docker build -t <IMAGE_NAME> .`

List the images in the local Docker repository:

`docker images`

To run a new container using the image:

`docker run -p 8181:8080 -d <IMAGE_NAME>`

The *-p* option maps port 8181 of the host to port 8080 of the container. The *-d* option causes the container to be run in the background and to output a long identifier that is the unique identifier of the container.

To list running containers:

`docker ps`

Stop the container with:

`docker stop <CONTAINER_ID>`

Confirm that the container has stopped by running `docker ps` again and verifying the absence of the container's process in the list.

To remove the container:

`docker container rm <CONTAINER_ID>`

To remove the image:

`docker rmi <IMAGE_NAME>`