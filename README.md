# InfraSecOps Studies

### Table of Contents
- [Docker](#docker)

### Examples
- [Laravel Example App](laravel-example-app)

## Docker

> ### Containers
-	OS (like linux distributions) are separated in OS kernels and software
-	Docker cannot run other distribution that is not a kernel running in host (machine),
	so they only can run for example, if your machine is a Manjaro Linux or MacOS, the machine
	kernel will be able to run another linux kernel-based images, but will not be able to
	run Windows images. Because windows systems are not compatible with the machine kernel host.

> ### Virtual Machines x Containers
-	VMs differently of containers, has the OS inside the VM and are shipped with this
- Containers will have a different approach just giving the dependencies, libraries and the
	application

> ### Images & Containers
-	These two are different
	- Images are the base of the structure, package, template and plan
	- Containers are the running `image` 

> ### Commands
-	Run: `docker run nginx` if the image does not exists, docker will pull from dockerhub

-	List: `docker ps` will list all containers that are running, that will show the id, image, command, etc. `docker ps -a` will list all containers, running or not

-	Stop: `docker stop container_name` will stop the container

-	Remove: `docker rm container` will remove the container

-	Images: `docker images` will list all images and your metadata

-	Remove image: `docker rmi image` will remove the image

-	Pull: `docker pull image` will download an image

- A container only lives until the process lives, if a process (like nginx) are killed, or crashed, the container dies.

-	Exec: `docker exec container_name cat /etc/hosts` will execute a command inside container

> ### Example of a Dockerfile

```dockerfile
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install python

RUN pip install flask
RUN pip install flask-mysql

COPY . /app/source_code

ENTRYPOINT FLASK_APP=/app/source_code/app.py flask run
```

