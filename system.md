# Docker system

## Install docker
```
curl -fsSL https://get.docker.com/ | bash
docker version
```

# Docker system commands
```
docker system df                            # show docker disk usage
docker system df -v                         # show detailed docker disk usage
docker system events                        # get real time events from the server
docker system events --since 2022-06-01     # events from 2022-06-01
docker system info                          # display system-wide information/daemon
docker system prune                         # remove unused data
```

# Read env variables on Linux
```
eval "$(<env.sh)"
```

# Linux kernel modules
## Namespaces (process isolation)
docker uses a technology called namespaces to provide the isolated workspace called the container. These namespaces provide a layer of isolation. each aspect of a container runs in a separate namespace and its access is limited to that namespace

## Control groups, cgroups (resources isolation)
cgroups is a linux kernel feature that limits, accounts for, and isolates the resource usage (cpu, memory, disk i/o, network, etc) of a collection of processes.

## Netfilter
Kernel module responsible for create ports, redirects and many package route tasks

# Logging drivers in docker
- json-file
- none
- syslog
- local
- journald
- splunk
- awslogs

The docker logs command is not available for drivers other than json-file and journald

# Docker daemon configuration paths
```
/etc/docker/daemon.json       # docker configuration file
/var/lib/docker               # docker home
```