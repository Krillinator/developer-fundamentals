---
icon: LiTerminal
---
# Docker Command Reference

A quick reference for commonly used Docker and Docker Swarm commands.

---

## Containers

| Command | What it does |
|---|---|
| `docker container run <image>` | Create and start a container from an image |
| `docker container run -d <image>` | Run a container in the background |
| `docker container run -p 8080:80 <image>` | Run a container and publish a port |
| `docker container ls` | Show running containers |
| `docker container ls -a` | Show all containers, including stopped ones |
| `docker container stop <container>` | Stop a running container |
| `docker container start <container>` | Start an existing stopped container |
| `docker container restart <container>` | Restart a container |
| `docker container rm <container>` | Remove a stopped container |
| `docker container logs <container>` | Show logs from a container |
| `docker container exec -it <container> sh` | Open a shell inside a running container |

---

## Images

| Command | What it does |
|---|---|
| `docker image ls` | Show locally stored images |
| `docker image pull <image>` | Download an image |
| `docker image build -t <name> .` | Build an image from a Dockerfile |
| `docker image rm <image>` | Remove an image |
| `docker image history <image>` | Show the history/layers of an image |
| `docker image prune` | Remove unused dangling images |

---

## Docker Hub / Registry

| Command | What it does |
|---|---|
| `docker login` | Sign in to a registry |
| `docker logout` | Sign out |
| `docker image tag <image> <username>/<repo>:<tag>` | Give an image a registry-compatible name/tag |
| `docker image push <username>/<repo>:<tag>` | Upload an image |
| `docker image pull <username>/<repo>:<tag>` | Download an image |

---

# Docker Swarm

## Swarm Management

| Command | Where? | What it does |
|---|---|---|
| `docker swarm init --advertise-addr <IP>` | Manager | Create a new Swarm |
| `docker swarm join --token <token> <manager-IP>:2377` | Joining node | Join an existing Swarm |
| `docker swarm leave` | Worker | Leave the Swarm |
| `docker swarm leave --force` | Manager | Force a manager to leave the Swarm |
| `docker swarm join-token worker` | Manager | Show the worker join command/token |
| `docker swarm join-token manager` | Manager | Show the manager join command/token |
| `docker swarm update` | Manager | Update Swarm-level configuration |

---

## Nodes

| Command | What it does |
|---|---|
| `docker node ls` | Show nodes in the Swarm |
| `docker node inspect <node>` | Show detailed information about a node |
| `docker node rm <node>` | Remove an inactive node from the Swarm |
| `docker node update --availability drain <node>` | Stop the node from receiving tasks and move existing tasks away |
| `docker node update --availability active <node>` | Make a node available for tasks again |

> [!NOTE]
> `docker node` management commands are run from a **manager node**.

---

## Services

| Command | What it does |
|---|---|
| `docker service ls` | Show services in the Swarm |
| `docker service create --name <name> <image>` | Create a service |
| `docker service ps <service>` | Show the tasks belonging to a service |
| `docker service inspect <service>` | Show detailed service configuration |
| `docker service rm <service>` | Remove a service |
| `docker service logs <service>` | Show logs across the service's tasks |

---

## Scaling

| Command | What it does |
|---|---|
| `docker service update --replicas 3 <service>` | Change the desired number of replicas to 3 |
| `docker service scale <service>=3` | Shorter way to scale a service to 3 replicas |

Example:

```bash
docker service update --replicas 3 nginx-web
```

This changes the **desired state**. The Swarm manager then schedules tasks until three replicas are running.

---

## Rolling Updates

| Command | What it does |
|---|---|
| `docker service update --image <image>:<tag> <service>` | Roll out a different image version |
| `docker service update --update-parallelism 1 <service>` | Update one task at a time |
| `docker service update --update-delay 10s <service>` | Wait 10 seconds between update batches |

Example:

```bash
docker service update \
  --image nginx:1.28 \
  nginx-web
```

Swarm gradually replaces the old tasks with new tasks using the new image.

---

## Watching Swarm

| Command | What it does |
|---|---|
| `watch -n 1 docker service ps <service>` | Refresh task status every second |
| `watch -n 1 docker service ls` | Refresh service status every second |
| `docker service ps <service>` | See current and previous tasks |
| `docker node ls` | Check the state of Swarm nodes |

Stop `watch` with:

```text
Ctrl + C
```

---

# Docker Storage & Cleanup

## Check Disk Usage

| Command | What it does |
|---|---|
| `docker system df` | Show how much disk space Docker uses |
| `docker system df -v` | Show detailed Docker disk usage |
| `df -h` | Show disk usage for all mounted filesystems |
| `df -h /` | Show disk usage for the main filesystem |

For our Ubuntu VMs, this is especially useful:

```bash
df -h /
```

Example:

```text
Size   Used   Avail   Use%
9.8G   5.7G   3.7G    61%
```

---

## Cleanup

| Command | What it removes |
|---|---|
| `docker container prune` | Stopped containers |
| `docker image prune` | Dangling images |
| `docker builder prune` | Unused build cache |
| `docker system prune` | Several types of unused Docker data |

> [!WARNING]
> Be careful with aggressive cleanup commands such as:
>
> ```bash
> docker system prune -a --volumes
> ```
>
> They can remove significantly more data, including unused images and volumes.

---

# Useful Linux Commands for Our Docker VMs

| Command | What it does |
|---|---|
| `hostname` | Show the machine's hostname |
| `hostname -I` | Show the machine's IP address |
| `groups` | Show which groups the current user belongs to |
| `ping -c 3 <IP>` | Test connectivity to another machine |
| `df -h /` | Check available disk space |
| `ip addr` | Show network interfaces and IP addresses |

---

# Quick Swarm Troubleshooting

| Question | Command |
|---|---|
| Is this machine part of a Swarm? | `docker info` |
| What nodes are in my Swarm? | `docker node ls` |
| What services are running? | `docker service ls` |
| Are all replicas running? | `docker service ls` |
| Where are my replicas running? | `docker service ps <service>` |
| What containers actually exist on this node? | `docker container ls -a` |
| What happened to this service's old tasks? | `docker service ps <service>` |
| How much space is Docker using? | `docker system df` |
| How much disk space does my VM have left? | `df -h /` |
| I forgot the worker join token. | `docker swarm join-token worker` |

