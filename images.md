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

## CMD
You can set default commands with parameters using the CMD instruction. If you run a Docker container without specifying any additional CLI commands, the CMD instruction will be executed. You can include an executable as the default command.

```
CMD echo "Command in Shell Form"
CMD ["/bin/bash", "-c", "echo Command in Exec Form"]
```

## ENTRYPOINT
The best use for ENTRYPOINT is to set the image's main command.
ENTRYPOINT doesn't allow you to override the command

The ENTRYPOINT instruction looks almost similar to the CMD instruction. However, the main highlighting difference between them is that it will not ignore any of the parameters that you have specified in the Docker run command (CLI parameters).

When we have specified the ENTRYPOINT instruction in the executable form, it allows us to set or define some additional arguments/parameters using the CMD instruction in Dockerfile. If we have used it in the shell form, it will ignore any of the CMD parameters or even any CLI arguments.

```
ENTRYPOINT echo "Command in Shell Form"
ENTRYPOINT ["/bin/bash", "-c", "echo Command in Exec Form"]
```

If use ENTRYPOINT and CMD in the same Dockerfile you must use both commands with exec form

```
ENTRYPOINT ["COMMAND1"]
CMD ["COMMAND2"]
```

## CMD vs ENTRYPOINT
Using a CMD command, you will be able to set a default command. This will be executed if you run a particular container without specifying some command. In case you specify a command while running a docker container, the default one will be ignored. Note that if you specify more than one CMD instruction in your dockerfile, only the last one will be executed.

```
CMD <command> parameter1, parameter2 …. (Shell form)
CMD [“executable command”, “parameter1”, “parameter2”, ….] (executable form)
CMD [“parameter1”, “parameter2”]
```

The third way is used to set some additional default parameters that will be inserted after the default parameters when you are using an ENTRYPOINT in executable form and if you run the container without specifying any arguments in the command line.

Consider the command below inside a dockerfile.

CMD echo “TutorialsPoint”
The output if you run it without specifying arguments (docker run -it image_name) will be -

TutorialsPoint
If you run it by specifying CLI arguments (docker run -it image_name /bin/bash), it will simply open a bash.

ENTRYPOINT looks similar to a CMD command. However, the difference is that it does not ignore the parameters when you run a container with CLI parameters. The two forms of ENTRYPOINT command are -

```
ENTRYPOINT <command> parameter1, parameter2, … (It is shell form)
ENTRYPOINT [“executable command”, “parameter1”, “parameter2”, ….] (Executable form)
```

When you use an executable form of ENTRYPOINT command, it will allow you to set additional parameters using CMD command. If you use it in shell form, it will ignore CMD parameters or any CLI arguments.

Consider the example below.

```
ENTRYPOINT [“/bin/echo”, “ENTRYPOINT_INSTRUCTION”]
CMD [“CMD_INSTRUCTION”]
```

If you try to run the container without specifying any CLI arguments (docker run -it image_name), output will be - "ENTRYPOINT_INSTRUCTION CMD_INSTRUCTION"

If you specify CLI arguments (docker run -it image_name NewParameter), output will be - "ENTRYPOINT_INSTRUCTION NewParameter".

To conclude, if you want to specify default arguments and want it to be overwritten on specifying CLI arguments, use CMD commands. And if you want to run a container with the condition that a particular command is always executed, use ENTRYPOINT. RUN is simply used to build additional image layers over the base image.

## WORKDIR
Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile

## ENV
Sets the environment variable <key> to the value <value>.

Example:
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

# Examples
  - [ex001](./config_files/images/ex001/)
  - [ex002](./config_files/images/ex002/)
  - [ex003](./config_files/images/ex003/)
  - [ex004](./config_files/images/ex004/)
  - [ex005](./config_files/images/ex005/)
  - [ex006](./config_files/images/ex006/)