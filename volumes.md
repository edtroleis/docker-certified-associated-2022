# Volumes

## No docker existem dois tipos de volumes: tipo volume e tipo bind
- tipo bind: já possuo um diretório e quero que esse diretório seja montado no container
```
mkdir /opt/dir1
docker container run -it --mount type=bind,src=/opt/dir1:dest=/opt/dir1 ubuntu /bin/bash
```

- tipo volume
```
docker volume ls
docker volume create vol1
docker volume inspect vol1
ls -ltr /var/lib/docker/volumes
docker container run -it --mount type=volume,src=vol1,dst=/dir1 ubuntu
docker volume rm vol1
```

## Local dos volumes
### Windows
```
\\wsl$\docker-desktop-data\version-pack-data\community\docker\volumes
```

### Linux
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

## Remover volume
```
docker volume rm VOLUME_NAME
docker volume prune                         # Remover volume que não estiver sendo utilizado por container
```

## Postresql com volume
```
docker volume create dbdados

docker container run -d -p 5432:5432 --name postgres1 --mount type=volume,src=dbdados,dst=/data -e POSTGRESQL_USER=docker -e POSTGRES_PASSWORD=docker -e POSTGRESQL_DB=docker postgres

docker container run -d -p 5433:5432 --name postgres2 --mount type=volume,src=dbdados,dst=/data -e POSTGRESQL_USER=docker -e POSTGRES_PASSWORD=docker -e POSTGRESQL_DB=docker postgres
```

## Volume backup
```
mkdir /opt/backup
docker container run -it --mount type=volume,src=dbdados,dst=/data --mount type=bind,src=/opt/backup,dst=/backup debian tar -zcvf /backup/bkp.tar.gz /data
```

## Container calling another volume container

```
docker run -d --name analytics --mount type=volume,src=data,dst=/data nginx
docker run -d --name reports --volumes-from=analytics nginx
```