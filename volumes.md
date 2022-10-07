# Volumes

On Docker there are two types of volumes: volume and bind

## Bind

I have a directory and I want this directory to be mounted in the container. The filesystem isn't managed by Docker.

```
mkdir /opt/dir1
docker container run -it --mount type=bind,src=/opt/dir1,dest=/opt/dir1 ubuntu /bin/bash
```

## Volume

Filesystem managed by Docker

```
docker volume ls
docker volume create vol1
docker volume inspect vol1
ls -ltr /var/lib/docker/volumes
docker container run --rm -it --mount type=volume,src=vol1,dst=/dir1 ubuntu            # the volume will be removed after container execution
docker volume rm vol1
```

# Volumes path

## Windows (WSL)

```
\\wsl$\docker-desktop-data\version-pack-data\community\docker\volumes
```

## Linux

```
docker volume inspect vol1

[
    {
        "CreatedAt": "2022-05-26T21:16:46Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/vol1/_data",
        "Name": "vol1",
        "Options": {},
        "Scope": "local"
    }
]
```

## Remove volume

```
docker volume rm VOLUME_NAME
docker volume prune                         # remove unused volumes by containers
```

## Example - Postresql container with volume

```
docker volume create dbdados
docker container run -d -p 5432:5432 --name postgres1 --mount type=volume,src=dbdados,dst=/data -e POSTGRESQL_USER=docker -e POSTGRES_PASSWORD=docker -e POSTGRESQL_DB=docker postgres
docker container run -d -p 5433:5432 --name postgres2 --mount type=volume,src=dbdados,dst=/data -e POSTGRESQL_USER=docker -e POSTGRES_PASSWORD=docker -e POSTGRESQL_DB=docker postgres
```

## Volume backup

```
mkdir /opt/backup
docker container run -it --mount type=volume,src=dbdados,dst=/data --mount type=bind,src=/opt/backup,dst=/backup ubuntu tar -zcvf /backup/bkp.tar.gz --absolute-names /data
```

## Container calling another volume container

```
docker volume create data
docker run -d --name analytics --mount type=volume,src=data,dst=/data nginx
docker run -d --name reports --volumes-from=analytics nginx
```
