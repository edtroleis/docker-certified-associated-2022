# Docker Containers

## Create and run a container

```
docker container run -p 8080:80 -d nginx

localhost:8080                                        # nginx page
```

## List containers

```
docker container ls                                   # show execution containers. Old version -> docker ps
docker container ls -a                                # list all containers. Old version -> docker ps -a
```

## Create, run and exec commands

```
docker container run hello-world                      # create and run a container, if doesn't exit the image hello-world this is downloaded from docker registry. Old version -> docker run hello-world
docker container run -ti centos                       # create and run the container with terminal
docker container run -ti ubuntu                       # create and run the container with terminal
docker container run -d nginx                         # create and run the container as a daemon (background)
docker container run --rm -it centos /bin/bash        # create and run the container with command /bin/bash, when the container is stopped the container is removed
docker run --rm -v /foo busybox                       # volume will be removed automatically once the container has exited
docker container run -d -m 128M --cpus 0.5 nginx      # create, run and set resource limit to the container
docker container create ubuntu                        # only create the container
docker container exec -ti CONTAINER_ID COMANDO        # run a command in a running container
```

## Connect to container main process

```
docker container attach CONTAINER_ID                  # connect to container main process. Container must be running
```

## Stop, start, restart, pause and unpause

```
docker container start CONTAINER_ID                   # start container
docker container stop CONTAINER_ID                    # stop container
docker container restart CONTAINER_ID                 # restart container
docker container pause CONTAINER_ID
docker container unpause CONTAINER_ID
```

## Inspect, stats and top

```
docker container inspect CONTAINER_ID               # show container configuration
docker container stats CONTAINER_ID                 # show container statistics
docker container top CONTAINER_ID                   # show container process
```

## Container logs

```
docker container logs -f CONTAINER_ID               # show continuous container logs
docker container logs CONTAINER_ID                  # show container logs
```

## Remove container

```
docker container rm CONTAINER_ID                    # remove container
docker container rm -f CONTAINER_ID                 # remove container with force option
docker container prune                              # remove all stopped containers
```

## Update container configurations

```
docker container update --memory 64M --cpus 0.4 nginx         # update container limits. Every container configuration can be updated
docker container update -m 128M --cpus 0.4 nginx
```

## Container restart policies

By default, Docker containers will not start when they exit or when docker daemon is restarted.
Docker provides restart policies to control whether your containers start autommatically when they exit, or when Docker restarts

```
no:             do not automatically restart the container (default)
on-failure:     restart the container if it exits due to an error, which manifests as a non-zero exit code
unless-stopped: restart the container unless it is explicitly stopped or Docker itself is stopped or restarted
always:         always restart the container if it stops
```

```
docker run -d --restart unless-stopped redis
docker update --restart unless-stopped redis
docker update --restart unless-stopped $(docker ps -q)
```

## Exit container without kill it

```
ctrl +pq
```

# Container stress test

```
docker container run -d nginx
docker container exec -it CONTAINER_ID /bin/bash
apt-get update && apt-get install -y stress
stress --cpu 2 --io 4 --vm 2 --vm-bytes 128M --timeout 10s
```

## Network

```
docker container create --dns=IP_ADDRESS        # create a container with custom DNS server
```

## Some commands

```
docker ... --driver ... IMAGE_NAME
docker ... --name ... IMAGE_NAME
docker ... --rm ... IMAGE_NAME
docker ... --volume ... IMAGE_NAME        # -v
docker ... --network ... IMAGE_NAME
docker ... --memory ... IMAGE_NAME        # -m
docker ... --cpus ... IMAGE_NAME
docker ... --publish ... IMAGE_NAME       # -p
docker ... --mount ... IMAGE_NAME
```

## A container calling another volume container

```
docker run -d --name analytics --mount type=volume,src=data,dst=/data nginx
docker run -d --name reports --volumes-from=analytics nginx
```

# Reservation and limits

By default, a container has no resource constraints and can use as much of a given resource as the host's kernel scheduler allows

It is important not to allow a running container to consume too much of the host machine's memory

On linux hosts, if the kernel detects that there is not enough memory to perform important system functions, it throws an "out of memory" exception and starts killing processes to free up memory

Limit imposes an upper limit to the amount of memory that can potentially be used by a docker container, reservations on the other hands is less rigid.
When the system is running low on memory and there is contention, reservation tries to bring the container's memory comsuption at or below the reservation limit.

Resource limit is hard limit for your service, while a reservation is used to find a host with adequate resources for scheduling.

```
docker container run -it --name container1 -m 256m nginx /bin/bash                      # maximum amount of memory container can use, hard limit
docker container run -it --name container1 --memory-reservation 1024m nginx /bin/bash   # --memory-reservation < --memory, soft limit, try to keep this ammount of memory at least

docker container run -it --name container1 --cpus=1.5 nginx /bin/bash                   # maximum amount of cpu container can use
docker container run -dt --name container1 --cpuset-cpus=0,1 nginx /bin/bash            # maximum amount of cpu-set container can use

free -m                                                                                 # check linux memory
```

## Docker commit

By default, the container being committed and its processes will be paused while the image is committed.

```
docker container commit CONTAINER_ID NEW_IMAGE_NAME
```
