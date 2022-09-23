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

ADD lets you to the same, but it also suports 2 other sources.

- you can use a url instead of a local file/directory
- you can extract a tar file from the source directly into the destination

## EXPOSE

It informs Docker that the container listens on the specified network port at runtime. It doesn't actually publish the port.

## HEALTHCHECK

It allows to tell the plataform on how to test that our application is healthy. When Docker starts a container, it monitors the process that the container runs. If the process ends, the container exits. That's just a basic check and does not necessarily tell the detail about the application.

--interval=DURATION (default 30s)

--timeout=DURATION (default 30s)

--start-period=DURATION (default 0s)

--retries=N (default 3)

```
HEALTHCHECK --interval=60s --timeout=3s --start-period=0 --retries=3 CMD curl -f http://localhost/ exit 1
```

Suppose we have a simple Web service. We want to add a health check to determine if its web service is working. We can use curl to help determine the HEALTHCHECK of its Dockerfile:

```
FROM nginx:1.13
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -fs https://www.google.com.br || exit 1
EXPOSE 80
```

Here we set a check every 30 seconds, if the health check command does not respond for more than 3 seconds, it is considered a failure, and use "curl -fs http://localhost/ || exit 1" as a health check command.

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

If you use ENTRYPOINT and CMD in the same Dockerfile you must use both commands with exec form

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

The third way is used to set some additional default parameters. That will be inserted after the default parameters when you are using an ENTRYPOINT in executable form and if you run the container without specifying any arguments in the command line.

Consider the command below inside a dockerfile.
```
CMD echo “TutorialsPoint”
```
The output if you run it without specifying arguments (docker run image_name) will be "TutorialsPoint".

If you run it by specifying CLI arguments (docker run -it image_name /bin/bash), it will simply open a bash.

ENTRYPOINT looks similar to a CMD command. However, the difference is that it does not ignore the parameters when you run a container with CLI parameters. The two forms of ENTRYPOINT command are:

```
ENTRYPOINT <command> parameter1, parameter2, …                    # shell form
ENTRYPOINT [“executable command”, “parameter1”, “parameter2”, ….] # executable form
```

When you use an executable form of ENTRYPOINT command, it will allow you to set additional parameters using CMD command. If you use it in shell form, it will ignore CMD parameters or any CLI arguments.

Consider the example below.

```
ENTRYPOINT [“/bin/echo”, “ENTRYPOINT_INSTRUCTION”]
CMD [“CMD_INSTRUCTION”]
```

If you try to run the container without specifying any cli arguments (docker run -it image_name), output will be "ENTRYPOINT_INSTRUCTION CMD_INSTRUCTION"

If you specify cli arguments (docker run -it image_name NewParameter), output will be - "ENTRYPOINT_INSTRUCTION NewParameter".

To conclude, if you want to specify default arguments and want it to be overwritten on specifying CLI arguments, use CMD commands. And if you want to run a container with the condition that a particular command is always executed, use ENTRYPOINT.

## RUN 
Execute commands and build additional image layers over the base image.

## WORKDIR
Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile.

## ARG
Sets arguments to build image
```
ARG NGINX-1.2
RUN curl -SL http://example.com/web-$NGINX.tar.gz
RUN tar -zxvf web-$NGINX.tar.gz
```

## ENV
Sets the environment variable <key, value> on container

You can use the -e, --env and --env-file flags to set simple environment variables in the container you're running, or overwrite variables that are defined in the Dockerfile of the image you're running

```
docker run --env VAR1=value1 --env VAR2=value2 ubuntu env | grep VAR
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

# Examples

- [ex001](./config_files/images/ex001/)
- [ex002](./config_files/images/ex002/)
- [ex003](./config_files/images/ex003/)
- [ex004](./config_files/images/ex004/)
- [ex005](./config_files/images/ex005/)
- [ex006](./config_files/images/ex006/)
- [ex007](./config_files/images/ex007/)
