# Docker Containers

## Criar e executar container nginx
```
docker container run -p 8080:80 -d nginx

localhost:8080 # página nginx
```

## Listar containers
```
docker container ls                                   # exibe containers em execução. Versão antiga -> docker ps
docker container ls -a                                # exibe todos containers. Versão antiga -> docker ps -a
```

## Criar e executar um container
```
docker container run hello-world                      # cria e executa container, se não existir a imagem hello-world a imagem é baixada do docker registry. Versão antiga -> docker run hello-world
docker container run -ti centos                       # cria e executa container com terminal
docker container run -ti ubuntu                       # cria e executa container com terminal
docker container run -d nginx                         # cria e executa container como daemon (background)
docker container run --rm -it centos /bin/bash        # cria e executa comando /bin/bash no container, após execução o container é removido
docker run --rm -v /foo busybox                       # volume will be removed automatically once the container has exited
docker container run -d -m 128M --cpus 0.5 nginx      # cria e define limite de memória e CPU para o container
```

## Apenas criar um container
```
docker container create ubuntu
```

## Conectar ao processo principal do container
```
docker container attach CONTAINER_ID                  # conecta-se ao processo principal do container, porém o container deve estar em execução
```

## Executar container
```
docker container exec -ti CONTAINER_ID COMANDO        # executa container e executa comando
```

## Stop, start, restart, pause e unpause de containers
```
docker container start CONTAINER_ID                   # inicia container
docker container stop CONTAINER_ID                    # para container
docker container restart CONTAINER_ID                 # reinicia container
docker container pause CONTAINER_ID
docker container unpause CONTAINER_ID
```

## Exibir configurações, estatíticas e processos do container
```
docker container inspect CONTAINER_ID               # exibe configuração do container
docker container stats CONTAINER_ID                 # exibe estatísticas do container
docker container top CONTAINER_ID                   # exibe processos do container
```

## Exibir logs do container
```
docker container logs -f CONTAINER_ID               # exibe logs do container
```

## Remover container
```
docker container rm CONTAINER_ID                    # remove container
docker container rm -f CONTAINER_ID                 # força a remoção do container 
docker container prune                              # remove todos containers parados
```

## Atualizar configurações do container
```
docker container update --memory 64M --cpus 0.4 nginx         # atualiza limite de memória e CPU do container. Pode ser feito o update de qualquer configuração do container
docker container update -m 128M --cpus 0.4 nginx              # atualiza limite de memória e CPU do container
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

## Sair do container sem terminá-lo
```
ctrl +pq
```

# Realizar stress test de containers

## Instalação e execução da ferramenta de stress
```
docker container run -d nginx
docker container exec -it CONTAINER_ID /bin/bash
apt-get update && apt-get install -y stress
stress --cpu 1 --vm-bytes 128M --vm 1
```

## Análise do container. Em outro terminal verifique as statísticas do container
```
docker container stats CONTAINER_ID
```

## Network
```
docker container create --dns=IP_ADDRESS        # create a container with custom DNS server
```

## Sintaxe
```
docker ... --driver ... IMAGE_NAME
docker ... --name ... IMAGE_NAME
docker ... --rm ... IMAGE_NAME
docker ... --volume ... IMAGE_NAME        # -v
docker ... --network ... IMAGE_NAME
docker ... --memory ... IMAGE_NAME
docker ... --cpu ... IMAGE_NAME
docker ... --publish ... IMAGE_NAME       # -p
docker ... --mount ... IMAGE_NAME
```

## A container calling another volume container
```
docker run -d --name analytics --mount type=voluem,src=data,dst=/data nginx
docker run -d --name reports --volumes-from=analytics nginx
```

# Reservation and limits
By default, a container has no resource constraints and can use as much of a given resource as the host's kernel scheduler allows

It is important not to allow a running container to consume too much of the host machine's memory

On linux hosts, if the kernel detects that there is not enough memory to perform important system functions, it throws an "out of memory" exception and starts killing processes to free up memory

Limit imposes an upper limit to the amount of memory that can potentially be used by a docker container, reservations on the other hands is less rigid
when the system is running low on memory and there is contention, reservation tries to bring the container's memory comsuption at or below the reservation limit

Resource limit is hard limit for your service, while a reservation is used to find a host with adequate resources for scheduling

```
docker container run -it --name container1 -m 256m nginx /bin/bash                      # maximum amount of memory container can use, hard limit
docker container run -it --name container1 --memory-reservation 1024m nginx /bin/bash   # --memory-reservation < --memory, soft limit, try to keep this ammount of memory at least

docker container run -it --name container1 --cpus=1.5 nginx /bin/bash                   # maximum amount of cpu container can use
docker container run -dt --name container1 --cpuset-cpus=0,1 nginx /bin/bash            # maximum amount of cpu-set container can use

free -m                                                                                 # check linux memory
```