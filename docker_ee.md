# What is UCP and DTR in Docker?
Docker trusted registry or simply Docker registry is an enterprise offering from Docker. The most common terminology that you will hear with Docker Enterprise Edition is DTR and UCP (universal control plane).
- minimum 3 nodes to achieve high-availability

# What is UCP in docker?
Docker Universal Control Plane (UCP) is the enterprise-grade cluster management solution from Docker. You install it on-premises or in your virtual private cloud, and it helps you manage your Docker cluster and applications through a single interface.

# Backup

## Order to backup Docker EE
1. backup swarm
2. ucp (do not save: overlay networks; configs, secrets; services)
3. dtr (do not save: image content; users, orgs, teams; database vulnerability)

## Backup UCP manager node
```
docker container run \
  --name ucp \
  --volume /var/run/docker.docker:/var/run/docker.sock \
  docker/ucp \
  backup [command options] > backup.tar
```

# Install Docker EE
- Url to installing Docker EE can be obtained from Docker Hub
- All nodes must be a worker node managed by Universal Control Plan (UCP)
- Endpoint to check UCP health: https://_ping
- UCP-agents runs on both manager and worker nodes

# Objects and definitions
- Grant = subject + role + resource set
- Subject = users + organizations
- UCP is responsible for managing access to cluster resources across your organization
- Roles define what operations can be done by whom
