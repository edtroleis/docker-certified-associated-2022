# Docker Images
Build once, use anywhere

- Docker image is a file which contains all necessary dependency and configurations which are required to run application
- Docker containers is basically a running instance an image

## Listar imagens
```
docker image ls                                       # exibe imagens docker. Versão antiga -> docker images
```

## Criar imagem a partir de Dockerfile

### Dockerfile exemplo
```
FROM ubuntu

LABEL app=ubuntu
LABEL maintainer="troleis"

ENV SUBSCRIPTION_NAME="dev"

RUN apt-get update && apt-get install -y stress && apt-get clean

CMD stress --cpu 0.2 --vm-bytes 64M --vm 1
```

### Build da imagem
```
docker image build -t edtroleis/image001:1.0.0 .
```

### Remover imagens
```
docker image prune                                        # remove todas imagens inválidas (dangling)
docker rmi IMAGE_NAME/IMAGE_ID
docker image rm IMAGE_NAME/IMAGE_ID
```

## COPY vs ADD
COPY takes in a src and destination. It only lets you copy in a local file or directory from our host

ADD lets you do that too, but it also suports 2 other sources.
- you can use a url instead of a local file/directory
- you can extract a tar file from the source directly into the destination

## EXPOSE
Informs Docker that the container listens on the specified network ports at runtime. It doesn't actually publish the port.

## HEALTHCHECK
Allows to tell the plataform on how to test that our application is healthy. When Docker starts a container, it monitors the process that the conainer runs. if the process ends, the containers exits. That's just a basic check and does not necessarily tell the detail about the application.

--interval=DURATION (default 30s)

--timeout=DURATION (default 30s)

--start-period=DURATION (default 0s)

--retries=N (default 3)

HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ exit 1

## ENTRYPOINT
The best use for ENTRYPOINT is to set the image's main command.
ENTRYPOINT doesn't allow you to override the command

## WORKDIR
Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile

## ENV
Sets the environment variable <key> to the value <value>.

Exemplo:
```
ENV NGINX 1.2
RUN curl -SL http://example.com/web-$NGINX.tar.gz
RUN tar -zxvf web-$NGINX.tar.gz
```

You can use the -e, --env and --env-file flags to set simple environment variables in the container you're running, or overwrite variables that are defined in the Dockerfile of the image you're running

```
docker run --env VAR1=value1 --env VAR2=value2 ubuntu env | grep VAR
```

## ARG e ENV
ARG: usado para a construção da imagem

ENV: usado para a criação do container

## Docker commit
By default, the container being committed and its processes will be paused while the image is committed.
```
docker container commit CONTAINER_ID IMAGE_NAME
```

## Image inspect
```
docker image inspect healthcheck --format='{{.Id}}'
docker image inspect healthcheck --format='{{json .ContainerConfig}}'
docker image inspect healthcheck --format='{{.ContainerConfig.Hostname}}'
```

## Flattening Docker images
```
docker export myubuntu > myubuntudemo.tar
cat myubuntudemo.tar | docker import - myubuntu:latest
```

## Moving images across hosts
```
docker save busybox > busybox.tar           # save one or more images to a tar file
docker load < busybox.tar                   # load an image from a tar file
```

## Docker content trust
Signing and verification of image tags

```
export DOCKER_CONTENT_TRUST     # sign an image before pushing it to the repository
```

# docker filter
```
docker image ls --filter "dangling=false"
```

Docker image is built up from a series of layers and each layer represents an instruction in the image's Dockerfile.
Entrypoint is a command that will be executed anytime a container is created using the image

