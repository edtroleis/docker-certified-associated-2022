# Network

Docker takes care of the networking aspects so that container can communicate with other containers and also with the Docker host
Docker networking subsystem is pluggable, using drivers:

- bridge: Connected directly to the physical network
- host: a private network shared with the host
- overlay
- macvlan: some on-premise network
- none

## Bridge network

Bridge network uses a software bridge which allows containers connected to the same bridge network to communicate, while providing isolation from containers which are not connected to that bridge network

- bridge is the default network driver for Docker
- If we don't specify a driver, this is the type of network you are creating
- When you start Docker, a default bridge network (also called bridge) is created automatically, and newly-started containers connect to it unless otherwise specified
- We also can create user-defined bridge network which are superior to the default bridge

### Difference between bridge network and user-defined bridge network

user-defined bridge network includes:

- provide better isolation and interoperability between containerized applications
- provide automatic dns resolution between containers
- containers can be attached and detached from user-defined networks on the fly
- each user-defined network creates a configurable bridge
- linked containers on the default bridge network share environment variables
- on Docker you only can create user-defined bridge network

```
docker network create --driver bridge mybridge
docker network ls
docker network inspect mybridge
ping mybridge
docker container run -dt --network mybridge ubuntu
```

## Host network

This driver removes the network isolation between the docker host and the docker containers to use the host's networking directly.
For instance, if you run a container which binds to port 80 and you use host networking, the container's application wiil be available on port 80 on the host's ip address

```
docker container run -dt --network host ubuntu
```

## None network

If you want to completely disable the networking stack on a container, you can use the none network.
This mode will not configure any ip for the container and doesn't have any access to the external network as well as for other containers

```
docker container run -dt --network none ubuntu
```

## Port binding

By default Docker containers can make connections to the outside world, but he outside world cannot connect to containers.
If we want to containers to accept incomming connection from the world, you wil have to bind it to a host port.

```
docker container run -d -p 8080:80 apache:1.0.0       # run container and attach host port 8080 with container port 80
docker container run -it -P                           # run container using container expose port and attach with host random port
```

# Links

- https://earthly.dev/blog/docker-networking/
- https://studygyaan.com/docker-tutorial/docker-networks
