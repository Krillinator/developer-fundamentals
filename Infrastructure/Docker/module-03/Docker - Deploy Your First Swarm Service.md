---
icon: LiNetwork
---
# Overview

![[undraw_code-deployed_iwvu.svg|281]]

We've created our first Docker Swarm.
But our Swarm isn't actually running an application yet.

We chose the machine, ran the command and Docker created the container on that machine.

Swarm introduces a different way of thinking about this.
Instead of telling an individual Docker Engine:

> **Run this container here.**

we tell the Swarm:

> **This is what I want running.**

To do that, we create a:

> **Service**

---

# Theory: What Is a Service?

A **service** describes a workload that we want Docker Swarm to maintain.

For example, we could tell the manager:

```text
Run NGINX
Keep 1 replica running
Expose it on port 80
```

The manager then decides **which available node should run it**.
We don't need to choose the worker ourselves.
That's the manager's job.

[Docker Docs - How services work](https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/)

---

# Deploy Our First Service

We'll use **NGINX**, a lightweight web server.

Run the following on our manager, `node-one`:

```bash
docker service create \
  --name nginx-web \
  --replicas 1 \
  --publish 80:80 \
  nginx
```

Docker creates a new service called:

```text
nginx-web
```

with a desired state of:

```text
1 replica
```

We didn't specify which node should run the container.
The **Swarm manager decides that for us**.


---

# Inspect the Service

Let's see what the Swarm is currently maintaining.

On `node-one`, run:

```bash
docker service ls
```

Look for:

```text
REPLICAS
1/1
```

This means:

```text
Desired: 1
Running: 1
```

Our desired state currently matches the actual state.

---

# From Service to Task to Container

There's another piece of Swarm terminology we need.
When the manager decides that our service needs one running instance, it creates a:

> **Task**

The task represents a piece of work assigned to a node.
That node then runs the container required by the task.

---

# Find Where NGINX Is Running

We didn't choose a node when we created the service.

So where did the manager put it?

Run:

```bash
docker service ps nginx-web
```

You should see something similar to:

```text
NAME          IMAGE          NODE        DESIRED STATE   CURRENT STATE
nginx-web.1   nginx:latest   node-one    Running         Running
```

Look at:

```text
NODE
```

That tells us which node received the task.
Managers coordinate the Swarm, but manager nodes can also run workloads by default.

Your result may be:

```text
node-one
```

```text
node-two
```

or:

```text
node-three
```

The important point is:

> **We didn't choose the node. The Swarm manager scheduled the task.**

---

# Look at the Actual Container

Suppose:

```bash
docker service ps nginx-web
```

shows:

```text
NODE
node-two
```

Go to `node-two` and run:

```bash
docker container ls
```

There you'll find the actual NGINX container created for the task.

> [!IMPORTANT]
> We didn't manually create this container with `docker container run`.
>
> Swarm created it because the **service's desired state required it**.

---

# Test the Service

Now let's send a request to NGINX.
From one of our Swarm nodes:

```bash
curl http://localhost
```

You should receive the default NGINX page as HTML.

But there's something interesting here.
Try the same command from:

```text
node-one
node-two
node-three
```

even if `docker service ps nginx-web` showed that the actual NGINX container is running on only **one** of them.

The request should still reach the service.

**How?**
When we created the service, we used:

```text
--publish 80:80
```

In Swarm mode, Docker can expose that published port across the nodes in the Swarm using its **routing mesh**.

A request can arrive at a Swarm node that isn't running the task.
Swarm can route that request to a node that **is** running the service.

That's why:

```bash
curl http://localhost
```

can work from each node even though we currently have only:

```text
1 replica
```

[Docker Docs - Swarm routing mesh](https://docs.docker.com/engine/swarm/ingress/)


---

# What Did We Learn?

We created our first Swarm service:

```text
nginx-web
```

and asked Docker Swarm to maintain:

```text
1 NGINX replica
```

The manager then:

1. Received our desired state.
2. Created a task for the service.
3. Selected an available node.
4. Assigned the task to that node.
5. The node started the NGINX container.

We also discovered another important Swarm feature:

> **The routing mesh allows a published service to receive traffic through Swarm nodes even when the container isn't running on that particular node.**

But we're still only running **one replica**.

That doesn't make very good use of our three-node cluster.

---

# What's Next?

We created a service with:

```text
1 replica
```

Next, we'll change its desired state.

We'll watch the manager schedule additional tasks across the Swarm and see how Docker distributes traffic between them.

This is where concepts such as **scaling, replicas and load balancing** start coming together.

Continue with: [[Docker - Scaling a Swarm Service]]