# InfraSecOps Studies

### Table of Contents
- [Docker](#docker)
- [Docker Compose](#docker-compose)
- [Kubernetes](#kubernetes)

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

- These points are structured on a layer architecture.
	- First Layer: is the base of the image (`FROM ubuntu:latest`)
	- Second Layer: is the update of the packages (`RUN apt-get update`)
	- Third Layer is the installation of dependencies for the application run (`RUN apt-get install python`)
	- Fourth and Fifth Layers are installation of dependencies
	- Sixth Layer: is the copy part of your code in your machine to the image directory (`COPY . /app/source_code`)
	- Seventh Layer: is the update of entrypoint for the command that will run (`ENTRYPOINT FLASK_APP ...`)

- The quantity of layers are based on how many commands in Dockerfile will be there. The layer architecure will be caching each step of this Dockerfile so when you need to append more layers, all layers back will be in cache

> ### CMD vs ENTRYPOINT 
- CMD will be the basic command for running the program so `CMD php artisan serve` will run the Laravel application and the container will be running this
- ENTRYPOINT is like the CMD but if you want to append new arguments to this command you can do something like:

```sh
docker run image_with_entrypoint coverage # coverage as some another argument for the entrypoint command
```

> ### Networking
When you install docker, it creates three default networks
- Bridge: Default network that the container will be attached. It is the private and internal network created by docker. Usually the container will receive an IP in the range of 172.17.0.0/24
- None: The containers are not attached to any network and does not any access to another containers, they run in a isolated network
- Host: Usually the port of container is already mapped to the external network

- We can create another networks by using `docker network create [ARGS]` passing `--driver bridge` and `--subnet [subnet IP]/16` and finally the name of the network, for example:
```sh
docker network create \
		--driver bridge \
		--subnet 182.18.0.0/16 \
		custom-network
```
- You can list all networks by using `docker network ls`
- NOTE: `docker inspect container_name` is your friend. Running this, you can see the configuration of the container

Example of a inspect from the `laravel-example-app`
```json
[
    {
        "Id": "8e9401507ace4059656a42986fdd45c07f1a5022586b436f7e9b4467cdec4962",
        "Created": "2023-01-28T05:17:38.137787004Z",
        "Path": "php",
        "Args": [
            "artisan",
            "serve",
            "--host=0.0.0.0",
            "--port=8000"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 5149,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-01-28T05:17:38.440918421Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:a1c95a996ff7b929d4a66d1fc3c4a4e37b3151a7f6054eacb28b76f7a7e3383c",
        "ResolvConfPath": "/var/lib/docker/containers/8e9401507ace4059656a42986fdd45c07f1a5022586b436f7e9b4467cdec4962/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/8e9401507ace4059656a42986fdd45c07f1a5022586b436f7e9b4467cdec4962/hostname",
        "HostsPath": "/var/lib/docker/containers/8e9401507ace4059656a42986fdd45c07f1a5022586b436f7e9b4467cdec4962/hosts",
        "LogPath": "/var/lib/docker/containers/8e9401507ace4059656a42986fdd45c07f1a5022586b436f7e9b4467cdec4962/8e9401507ace4059656a42986fdd45c07f1a5022586b436f7e9b4467cdec4962-json.log",
        "Name": "/busy_blackburn",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {
                "8000/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "8000"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/cdbf11a0991bb17da71afdad15559a18024ec729a144afce3ce5d12c1e2d8800-init/diff:/var/lib/docker/overlay2/tpx3e7qnlkx15tpt3z791rffr/diff:/var/lib/docker/overlay2/lqppzvwco27lwck3k5srmclna/diff:/var/lib/docker/overlay2/4npjuwx96f69euc94ybqr5pjk/diff:/var/lib/docker/overlay2/2bea3e281686d1e5e40ea346218dbc0f702488aca2841cc93a34ed268b58bd25/diff",
                "MergedDir": "/var/lib/docker/overlay2/cdbf11a0991bb17da71afdad15559a18024ec729a144afce3ce5d12c1e2d8800/merged",
                "UpperDir": "/var/lib/docker/overlay2/cdbf11a0991bb17da71afdad15559a18024ec729a144afce3ce5d12c1e2d8800/diff",
                "WorkDir": "/var/lib/docker/overlay2/cdbf11a0991bb17da71afdad15559a18024ec729a144afce3ce5d12c1e2d8800/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "8e9401507ace",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "3000/tcp": {},
                "8000/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/opt/bitnami/python/bin:/opt/bitnami/php/bin:/opt/bitnami/php/sbin:/opt/bitnami/node/bin:/opt/bitnami/common/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "OS_ARCH=amd64",
                "OS_FLAVOUR=debian-11",
                "OS_NAME=linux",
                "APP_VERSION=9.5.1",
                "BITNAMI_APP_NAME=laravel",
                "NODE_PATH=/opt/bitnami/node/lib/node_modules",
                "PHP_ENABLE_OPCACHE=0"
            ],
            "Cmd": [
                "serve",
                "--host=0.0.0.0",
                "--port=8000"
            ],
            "Image": "laravel-example-app",
            "Volumes": null,
            "WorkingDir": "/app",
            "Entrypoint": [
                "php",
                "artisan"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.authors": "https://bitnami.com/contact",
                "org.opencontainers.image.description": "Application packaged by Bitnami",
                "org.opencontainers.image.licenses": "Apache-2.0",
                "org.opencontainers.image.ref.name": "9.5.1-debian-11-r4",
                "org.opencontainers.image.source": "https://github.com/bitnami/containers/tree/main/bitnami/laravel",
                "org.opencontainers.image.title": "laravel",
                "org.opencontainers.image.vendor": "VMware, Inc.",
                "org.opencontainers.image.version": "9.5.1"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "eeaaa09840e1e8c4e98dd946f3f3e69964fe6c5e5bd6fd9dc8a9bde043df81e3",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "3000/tcp": null,
                "8000/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "8000"
                    }
                ]
            },
            "SandboxKey": "/var/run/docker/netns/eeaaa09840e1",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "1c575a47424e2cb964a28857a24602d25de0a97c9f8b3cecee22e673b1e2ef2e",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "3521ac1e7aa60c7a7d3cb44cf234a2be9887def483ad9b28bd1526fa2e63da49",
                    "EndpointID": "1c575a47424e2cb964a28857a24602d25de0a97c9f8b3cecee22e673b1e2ef2e",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

> ### Volumes
Docker can persist data in volums (one big example of this is, where your database will persist data, such mysql or redis), so for this you can run the following command:
```sh
docker volume create data_volume
```
This will createa a folder `data_volume` inside your machine to persist data from containers, so you can run your container mounting the volume: `docker run -v data_volume:/var/lib/mysql container_name`

## Docker Compose
This is how we can orchestrate docker container using YAML files.

```yaml
version: '3'

services:
	web:
		image: simple_web/application:latest
		port: 8000:8000
	database:
		image: postgresql:10.2
```
This is only applicable to running container in a single docker host, it means this is not for production.

## Kubernetes

Kubernetes is for container orchestration

> ### Cluster
A cluster is a set of `nodes` group together. If one node fails, other nodes will be available keeping your application working

> ### Node
A Node is a worker machine where will stay the containers

> ### Master
A master is a node that kubernetes control components, this will watcher over the nodes in cluster and it's responsible for the actual orchestration of containers in the worker nodes

> ### Components
- API Server
- Scheduler
- Controller
- Container Runtime
- kubelet 
- etcd
