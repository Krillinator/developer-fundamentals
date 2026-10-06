---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

We now have a new machine called `node-four`, but right now it is still just an independent Docker host.

Our existing Docker Swarm already contains:

```text
node-one
node-two
node-three
```

and our `nginx-web` service is configured to maintain **3 replicas**.

In this lab, we'll add `node-four` to the existing Swarm and then increase our service from **3 replicas to 4 replicas**.

> **The goal is simple: add a new worker to an existing Docker Swarm and scale our service to four replicas.**

---

# 1. Check Our Current Swarm

Start on the manager, `node-one`.

Check the nodes currently participating in the Swarm:

```bash
docker node ls
```

You should currently have three nodes:

```text
node-one
node-two
node-three
```

Now check our existing service:

```bash
docker service ls
```

Our `nginx-web` service should currently have:

```text
3/3
```

This means our **desired state is 3 replicas**, and all three are currently running.

---

# 2. Get the Worker Join Command

`node-four` needs the information required to join our existing Swarm.

On `node-one`, retrieve the worker join command:

```bash
docker swarm join-token worker
```

> [!IMPORTANT]
> The join token should be treated as a secret. Don't store the real token in your notes, Git repository or screenshots.

---

# 3. Add `node-four` to the Swarm

Move over to `node-four` and run the join command provided by the manager.

If successful, Docker should tell you:

```text
This node joined a swarm as a worker.
```

Return to `node-one` and verify the result:

```bash
docker node ls
```

You should now see:

```text
node-one
node-two
node-three
node-four
```

`node-four` should report a status of `Ready`.

We have successfully expanded our Swarm from **three nodes to four nodes**.

---

# 5. Scale the Service

We now have four nodes available, so let's change what we want from our service.

Update `nginx-web` from **3 replicas to 4 replicas**:

```bash
docker service update \
  --replicas 4 \
  nginx-web
```

We have now changed the desired state:

```text
Before: 3 replicas
After:  4 replicas
```

The manager compares this new desired state with the actual state. It sees that only three replicas are running, so **one additional task is needed**.

Swarm then schedules that task onto an eligible node.

---

# 6. Observe the Result

Check the service:

```bash
docker service ls
```

Once Swarm has finished, you should see:

```text
REPLICAS
4/4
```

Now inspect the individual tasks:

```bash
docker service ps nginx-web
```

Look at the `NODE` column.

Which node received the new task?

> [!NOTE]
> Swarm decides where tasks should run. Four replicas and four nodes does **not guarantee one replica per node**.
>
> `node-four` is now eligible to receive tasks, but the scheduler ultimately decides where the new task should run.

---

# 7. Verify the Container

Go to the node that received the new task and run:

```bash
docker container ls
```

You should find an NGINX container belonging to the `nginx-web` service.

This gives us the same three levels of verification we've used previously:

| Command | What are we checking? |
|---|---|
| `docker service ls` | Is the service maintaining `4/4` replicas? |
| `docker service ps nginx-web` | Which nodes are the tasks running on? |
| `docker container ls` | Is the actual container running on this node? |

---

# What Did We Practice?

This time, we didn't create a new Swarm. We **expanded an existing one**.

We added `node-four` as a worker and saw that simply adding more infrastructure doesn't change the desired state of an existing service. Our service remained at three replicas until we explicitly asked Swarm for four.

When we changed the desired state from:

```text
3 replicas
```

to:

```text
4 replicas
```

the manager detected that one replica was missing and scheduled another task.

> **Adding a node increases the resources available to the Swarm. Scaling a service changes how many instances of that workload we want Swarm to maintain.**

These are two separate operations.

---

# What's Next?

Our Swarm has grown:

```text
4 nodes
4 desired replicas
```

We've seen how worker nodes can **leave and join the Swarm**, and how the manager maintains the desired state of our services when the cluster changes.

But there's still one important weakness in our setup:

> **We only have one manager.**

Right now, `node-one` is solely responsible for coordinating the Swarm. Our workers give us multiple machines for running workloads, but the management side of our cluster still depends on a single node.

So what if we added another manager?

In the next lab, we'll prepare a new machine called `node-five` and join it to the Swarm using a **manager join token** instead of a worker token.

This will introduce us to **multiple managers, leadership and consensus** inside a Docker Swarm.

Continue with: [[Docker - Hands-On Docker Swarm Practice 09]]