# Docker Images

Build once, use anywhere

- Docker image is a file which contains all necessary dependency and configurations which are required to run application
- Docker containers is basically a running instance an image

## List images

```
docker image ls                                       # list docker images. Old version -> docker images
```

## Create image from Dockerfile

### Dockerfile exemplo

```
FROM ubuntu

LABEL app=ubuntu
LABEL maintainer="troleis"

ENV SUBSCRIPTION_NAME="dev"

RUN apt-get update && apt-get install -y stress && apt-get clean

CMD stress --cpu 2 --vm 1 --vm-bytes 64M
```

### Build image

```
docker image build -t edtroleis/image001:1.0.0 .
```

### Remove images

```
docker image prune                                        # remove all invalid images (dangling)
docker rmi IMAGE_NAME/IMAGE_ID
docker image rm IMAGE_NAME/IMAGE_ID
```

## Image inspect

```
docker image inspect IMAGE_NAME --format='{{.Id}}'
docker image inspect IMAGE_NAME --format='{{json .ContainerConfig}}'
docker image inspect IMAGE_NAME --format='{{.ContainerConfig.Hostname}}'
```

## Flattening Docker images

```
docker run --name ubuntu1 ubuntu
docker export ubuntu1 > ubuntu1.tar
cat ubuntu1.tar | docker import - myubuntu:latest
```

## Moving images across hosts

```
docker save busybox > busybox.tar           # save one or more images to a tar file
docker load < busybox.tar                   # load an image from a tar file
```

## Docker content trust

Docker Content Trust (DCT) provides the ability to use digital signatures for data sent to and received from remote Docker registries. These signatures allow client-side or runtime verification of the integrity and publisher of specific image tags.

```
export DOCKER_CONTENT_TRUST=1
```

# docker filter

```
docker image ls --filter "dangling=false"
```

# Local Docker registry

```
docker container run -d -p 5000:5000 --restart=always --name registry registry:2
```
