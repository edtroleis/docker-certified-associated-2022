# Orchestration

# Docker Swarm
Nodes
- worker: used to run general jobs
- manager: used to administration

Docker swarm manager nodes are responsible for handling the cluster management tasks (administration)

Manager node has many responsibility within swarm:
- maintaining cluster state
- scheduling services
- serving swarm mode http api endpoints
using a raft implementation, the managers maintain a consistent internal state of the entire swarm and all the services running on it
- Docker recommends you implement an odd number of nodes according to your organization's high-availability requirements
- an n manager cluster tolarates the loss of at most (n-1)/2 managers. Manager up nodes must be > 50% to cluster stay up.
- default address pool used by swarm for global scope overlay network: 10.0.0.1/8

```
docker swarm init                                     # start swarm
docker swarm init --advertise-addr MANAGER_IP         # create cluster swarm with explicity network interface
docker swarm init --advertise-addr 127.0.0.1          # create cluster swarm with explicity network interface

docker swarm leave                                    # node leaves the cluster
docker swarm leave --force                            # manager leaves the cluster

docker node promote NODE                              # set node as manager
docker node demote NODE                               # remove node as manager

docker swarm join-token worker                        # get token to add worker node
docker swarm join --token WORKER_TOKEN IP_ADDRESS     # add node as worker on cluster

docker swarm join-token manager                       # get token to add manager node
docker swarm join --token MANAGER_TOKEN IP_ADDRESS    # add node as manager on cluster

docker node ls                                        # list nodes
docker node update --label-add LABEL                  # add or update a node label

docker swarm ca --rotate                              # generate a new root CA certificate and root CA key for the swarm cluster
```

## Replicated Service
A service is a definition of tasks to execute on the manager or worker nodes.
Containers running in a service are called tasks. The default is replicated service.

```
docker service create --name webserver --replicas 1 nginx     # create a service with 1 replica
docker service ls                                             # list services
docker service ps webserver                                   # list task of webserver service
docker service scale webserver=5                              # scale to 5 replicas
docker service update --replicas 5 webserver                  # update replicas to 5
docker service rm webserver                                   # remove service

docker service logs -f SERVICE_NAME                           # show log from all containers

docker service rollback SERVICE_NAME                          # roll back to the previous version of a service

docker service create --limit-cpu 2 ...
docker service create --limit-memory 2048 ...

docker service create --name dns-cache -p 53:53/udp dns-cache # create a service listen on port 53 using the UDP protocol

docker service update --image nginx web-server                # image service will be updated to nginx
```

## Global service
A global service is a service that runs one task on every node
each time you add a node to the swarm, the orqhestrator creates a task and the scheduler assigns the task to the new node

```
docker service create --name antivirus --mode global -d ubuntu
docker service ps antivirus
```

## Draining swarm node
```
docker service create --name webserver --replicas 2 nginx     # create a service with 2 replicas
docker service ls                                             # list services
docker service ps webserver                                   # list task of webserver service
docker node update --availability drain NODE_NAME             # change the node Availability to Drain. os containers desse node serão recriados em outros nodes e o node não estará disponível
docker node update --availability active NODE_NAME            # change the node Availability To Active. ativa novamente o node

docker node update --availability {drain, pause, active}      # atualiza o status do node
- drain: remove todos os containers do node atual para um novo node
- pause: nada acontece com os containers presentes no node, porém nenhum container será criado no node
- active: node recebe novos containers

```

## Inspect swarm services and nodes
docker inspect provides detailed information of the Docker objects
- containers
- network
- volumes
- swarm services
- swarm nodes

```
docker service inspect SERVICE_NAME
docker service inspect SERVICE_NAME --pretty

docker node inspect NODE_NAME
docker node inspect NODE_NAME --pretty
```

## Publish port at service
```
docker service create --name webserver --replicas 2 nginx
docker service create --name webserver --replicas 2 --publish 8080:80 nginx
curl -v localhost:8080
```

## Docker stack
- A stack is a group of interrelated services that share dependencies and can be orchestrated and scaled together
- A stack can compose YAML file
- We can define everything within the YAML file that we migth define while creating a Docker service

- docker-compose.yaml file can be used by docker stack
- docker stack is more suited for production environment
- docker stack can be used to manage multiple services

```
docker stack deploy --compose-file docker-compose.yml stack1        # create a stack
docker stack ls                                                     # list stacks
docker stack rm stack1                                              # remove stack
```

## Backup
```
cp /var/lib/docker/swarm
docker swarm init --force-new-cluster
```

## Constraints and Preferences
```
docker service create \
  --name webserver \
  --replicas 5 \
  --constraint node.labels.region=east \
  nginx
```

```
docker service create \
  --name webserver \
  --replicas 5 \
  --placement-pref 'spread=node.labels=datacenter' \
  nginx
```

## Locking the swarm cluster
Swarm cluster contains lot of sensitive informartion.
- tls key used to encriypt communication among swarm nodes.
- keys used to encrypt and decrypt the raft logs on disk.
if your swarm is compromised and if data is sorted in plain-text, and attack can get all the sensitive information.
Docker lock allows up to have control over the keys

Setting up Autolock
```
docker swarm update --autolock=true
systemctl restart docker
docker node ls
```

To Retrieve Key after swarm is unlocked
```
docker swarm unlock                         # remove lock
docker swarm unlock-key
```

To rotate the key:
```
docker swarm unlock-key --rotate
```

## Mounting volumes with Swarm
```
docker service create --name service1 --mount type=volume,source=myvolume,target=/mypath nginx
docker volume ls
```

## Docker Swarm - import pointers for quorum
We should maintain an odd number of nodes within the cluster. We have to do at least 51% of node managers up in the cluster for the cluster stays up.

```
cluster size  majority  fault tolerance
1             0         0
2             2         0
3             2         1
4             3         1
5             3         2
6             4         2
7             4         3
```

## Overlay networks
- Overlay network driver creates a distributed network among multiple Docker daemon hosts
- Overlay network allows containers connected to it to communicate securely
- Used by Swarm ingress
- Overlay networks are first created on the manager nodes, then they are created on the worker nodes once a task is scheduled on the specific worker node

### Default networks in Swarm
- ingress (overlay): global scope, communication between swarm services
- docker_gwbridge (bridge): local scope, it connects the individual docker daemon to the other daemons participating in the swarm

### Ports
- TCP 2377: cluster management communication
- TCP and UDP 7946: communication among nodes
- UDP 4789: overlay network traffic

```
docker network create -d overlay -o encrypted=true                                          # overlay traffic between service tasks will be encrypted
docker service update --network-add                                                         # add a network to a service

docker network create -d overlay NETWORK_NAME                                               # cria uma rede
docker service create --network nome_da_rede nome_do_servico                                # cria um servico com uma rede
docker service update --network-add nome_da_rede nome_do_servico                            # adiciona uma rede a um determinado service
docker service create -p 80:80 --network my_overlay --constraint "node.role==worker" nginx  # creates a service using my_overlay network with constraints
```

### Create overlay network on Docker standalone
```
docker network create --driver overlay --attachable my_overlay_attachable
docker run --network my_overlay_attachable --name webserver nginx bash
```


## Secrets
- São usadas apenas em swarm
- São criadas apenas em manager nodes

```
docker secret create SECRET_NAME FILE_WITH_PWD        # create secret
docker secret ls                                      # list secrets
docker service create --secret SECRET_NAME nginx      # create service with secret
```

### Show secret into container
```
docker exec -it CONTAINER_NAME bash
cd /run/secrets
```

### Change secret from a service
```
docker service update --secret-rm SECRET_NAME SERVICE_NAME      # remove secret from service
docker secret rm SECRET_NAME                                    # remove secret
docker secret create NEW_SECRET_NAME FILE_WITH_PWD2
docker service update --secret-add NEW_SECRET_NAME SERVICE_NAME
```


# Docker compose
- It is well suited for development environments
- It will bring up services as specified in docker-compose.yml

```
docker-compose config                   # check file docker-compose.yaml
docker-compose up                       # start docker-compose
docker compose up -d                    # start docker compose dettached
docker compose up -d --build            # start docker compose creating a new image
docker-compose down                     # stop and remove containers belong to docker-compose.yaml file
docker compose ps                       # show docker composes running
docker-compose rm                       # remove configuration
docker compose exec app bash            # access container
docker compose exec -u root app bash    # access container with root user
```

### Examples
  - [ex001](./config_files/compose/ex001/)
  - [ex002](./config_files/compose/ex002/)
  - [ex003](./config_files/compose/ex003/)

### Notes
```
netstat -tupan                          # list network connections
whoami                                  # check current user on container
cat /etc/passwd                         # check users on container
command1 & command2                     # run commands together
command1 && command2                    # run command1 first and then command2
git config --global -l                  # list git config
```

# Kubernetes

## minikube
https://minikube.sigs.k8s.io/docs/start/

```
minikube start
minikube pause
minikube unpause
minikube stop
```

## Pods
```
kubectl run nginx --image=nginx       # criar um pod
kubectl get pods                      # exibir pods
kubectl get pods -w                   # exibir pods dinamicamente
kubectl exec -it nginx -- bash        # executar pod
kubectl delete pod nginx              # deletar um pod
kubectl get pods -l env=development   # list all the pods that have an environment variable env=development

kubectl apply -f manifest1.yaml       # deploy yaml manifest

kubectl api-resources                 # show apis

kubectl exec -it POD_NAME bash        # acessa o pod
kubectl logs POD_NAME
kubectl describe pod POD_NAME
```

## replicasets
A replicaset purpose is to maintain a stable set of replica pods running at any given time.
replicasets works well in basic functionality like managing pods, scaling pods and similar

```
kubectl apply -f https://kubernetes.io/examples/controllers/frontend.yaml
kubectl get replicaset
```

## Deployments
Deployments provides replication functionality with the help of replicasets, along with various additional capability like rolling out of changes, rollback changes if required

We can easily rollout new updates to our application using deployments

Deployments will perform update in rollout manner to ensure that your app is not down

Sometimes you may want to rollback a deployment, for example, when the deployment is not stable, such as crash looping

- deployment ensures that only a certain number of pods are down while they are being updated.
- by default, it ensures that at least 25% of the desired number of pods are up (25% max unavailable)
- deployments keep the history of revision which had been made

```
kubectl get deployments
kubectl apply -f deployment.yaml
kubectl get replicasets
kubectl rollout history deployment
kubectl rollout history deployment/deployment --revision 1
```

## Secrets

### Literal value
```
kubectl create secret generic secret1 --from-literal=key1=password1
```

### File
```
kubectl create secret generic secret2 --from-file=./credentials.txt
echo SECRET_ENCRYPTED | base64 -d   # show decripted password
```

### yaml file
```
1. echo -n 'user3' | base64
2. echo -n 'pwd3' | base64
3. copy the values above and paste on secrets.yaml
kubectl apply -f secret.yaml
```

## Volumes in kubernetes
One of the benefits of kubernetes is that it supports multiple types of volumes
- ebs
- cider
- glusterfs
- local
- nfs

## Ports
- targetPort: is the port the container is running on
- port: is the port the service is exposed on in the cluster
- nodePort: is the port made avaiable to external consumers of the service

# Examples
  - [Kubernetes files](./config_files/kubernetes/)
