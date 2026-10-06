---
icon: LiBook
---
# Overview

![[docker-swarm.png|415]]

So far, we've been working with Docker on **one machine**.
We build an image, start a container and Docker runs that container on our computer.

However, what happens if:
- the server becomes overloaded?
- we need more instances of the application?
- a container crashes?
- the entire machine goes offline?

Running everything on one machine gives us one machine to manage, but it also gives us **one machine to depend on**.

This is where **container orchestration** becomes useful.

---

# Recap: What Is Container Orchestration?

Imagine we have three machines running Docker:

```text
node-one        node-two        node-three
Docker          Docker          Docker
```

Without orchestration, they're simply three independent Docker hosts.

We decide manually:
- which machine runs each container,
- how many containers should run,
- what happens if one stops,
- where another container should start,
- and how workloads should be distributed.

As the environment grows, that becomes increasingly difficult to manage manually.

**Container orchestration** introduces a system responsible for coordinating those machines and the containers running across them.

Docker provides its own built-in orchestration system:

> **Docker Swarm**

[Docker Docs - Swarm mode](https://docs.docker.com/engine/swarm/)

---

# Recap: What Is a Docker Swarm?

A **Docker Swarm** is a cluster of machines running Docker Engine that work together.

Each machine participating in the Swarm is called a **node**.

![[node-summary-diagram-overview.png]]

Instead of thinking about three completely independent Docker hosts we can begin thinking about them as one cluster.
Docker Swarm coordinates how container workloads run across these nodes.

[Docker Docs - Swarm mode key concepts](https://docs.docker.com/engine/swarm/key-concepts/)


---

# Manager and Worker Nodes

Every machine that joins a Docker Swarm becomes a **node**.

A node can have one of two roles:

```text
Manager
Worker
```

These roles determine the responsibilities that machine has inside the Swarm.

Let's look at each one separately before putting them together.

---

## Manager Node

The **manager node coordinates the Swarm**.

Imagine we want our application to always have three instances running.

We don't connect to three different machines and manually start three containers.

Instead, we tell the manager what we want:

```bash
docker service create --replicas 3 app:latest
```


It then determines where the required tasks should run and schedules them onto available nodes.

![[manager-node-explained-diagram.png]]

The manager is therefore responsible for things such as:
- maintaining the state of the Swarm,
- receiving requests to create, update or remove services,
- deciding which nodes should run each task,
- receiving task-state updates from workers
- and working to maintain the desired state.

> **We tell the manager what we want. The manager coordinates how the Swarm achieves it.**

[Docker Docs - How nodes work](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/)

---

## Worker Node

A **worker node executes tasks assigned by a manager**.
The worker doesn't decide:

> *"I think I'll run another copy of the application."*

Instead, the manager assigns a task to the worker, and the worker executes that task.

![[worker-node-explained-diagram.png]]

The worker is responsible for:
- **receiving instructions from the manager about what to run,**
- **starting and running the assigned containers,**
- **keeping track of whether those containers are running, stopped or failed,**
- **and reporting that information back to the manager.**

This communication is important because the manager needs information about what's **actually happening** in the Swarm.

---

# Putting the Roles Together

Now we can combine the two roles.

Imagine our Swarm contains:

```text
1 Manager
3 Workers
```

We create a service and request three replicas:

```bash
docker service create --replicas 3 app:latest
```

The workflow looks roughly like this:

```text
1. We define what we want
2. Manager stores the desired state
3. Manager schedules tasks
4. Workers execute those tasks
5. Workers report task state
6. Manager continues monitoring the Swarm
```

![[docker-swarm-explained-diagram.png]]

If everything is working correctly, the manager sees that the desired state and actual state match.
At this point, the manager doesn't need to create another replica.

But what happens when something fails?

---

# When a Task Fails

Imagine our service still has this desired state:

```text
3 replicas
```

and initially we have:

```text
Worker 1     Worker 2     Worker 3
   |            |            |
Container    Container    Container
```

Everything matches:

```text
Desired: 3
Actual:  3
```

Now imagine the task on `Worker 2` fails.

The Swarm no longer has the state we requested:

```text
Desired: 3
Actual:  2
```

It therefore schedules a **replacement task** onto an available node.

![[docker-swarm-container-failure-schedule-replacement-diagram.png]]

So we're back to:

```text
Desired: 3
Actual:  3
```

This process is called **reconciliation**.

> **The manager compares the desired state with the current state and works to bring them back into agreement.**

Notice that the manager isn't repairing the failed container.
Instead, Swarm creates a **replacement task** to restore the service to its desired state.

[Docker Docs - How services work](https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/)

---

# Distributing the Work

Multiple nodes also give us somewhere to distribute our workloads.
Imagine one machine trying to run everything:

```text
Server
├── Web 1
├── Web 2
├── Web 3
├── API 1
├── API 2
└── Worker
```

CPU and memory all come from the same machine.
With multiple Docker hosts, the scheduler can place tasks across available nodes.

```text
node-one        node-two        node-three

Web 1           Web 2           Web 3
API 1           API 2           Worker
```

This is especially useful when working with cloud instances such as AWS EC2.

---

# Scaling

Suppose our application becomes busier.
Instead of manually creating containers on particular machines, we can change the desired number of replicas.

```text
3 replicas
```

could become:

```text
6 replicas
```

The Swarm manager then schedules the additional tasks across available nodes.
Likewise, we can scale the service back down when fewer instances are needed.

[Docker Docs - Deploy services to a Swarm](https://docs.docker.com/engine/swarm/services/)

---

# But What If the Manager Fails?

There's an important problem with our simple example.

Imagine our entire Swarm depends on one Manager:

![[docker-swarm-container-failure-schedule-replacement-diagram.png]]

If that is our **only manager** and it fails, the existing worker tasks can continue running, but the Swarm loses its ability to perform management operations until the manager is recovered.

We've created a:
> **Single Point of Failure**

A **single point of failure** is a component whose failure can make an important part of the system unavailable.

So although this is perfectly useful for our learning environment:

```text
1 Manager
2 Workers
```

it isn't the configuration we'd choose when manager availability is important.

[Docker Docs - Administer and maintain a Swarm](https://docs.docker.com/engine/swarm/admin_guide/)

---

# Multiple Managers

Docker Swarm can have multiple manager nodes.
For example:

```text
Manager 1
Manager 2
Manager 3
```

Managers maintain the Swarm's state using a consensus system called **Raft**.
For important decisions, a **majority of managers** must be available.
This majority is called a: **Quorum**

With three managers:

```text
3 managers
2 required for quorum
1 can fail
```

This allows the Swarm to continue managing the cluster if one manager becomes unavailable.

Docker recommends using an **odd number of managers** when configuring manager high availability.

> [!INFO]- Why Not Just Make Every Node a Manager?
> More managers don't automatically mean better performance.
>
> Managers need to communicate and agree on changes to the Swarm's state. Adding managers therefore adds coordination overhead.
>
> Manager count is a balance between **fault tolerance** and **coordination overhead**.

[Docker Docs - Maintain manager quorum](https://docs.docker.com/engine/swarm/admin_guide/#maintain-the-quorum-of-managers)

---

# Our Learning Environment

For our first Swarm, we'll deliberately keep things simple:

```text
Docker Swarm

node-one
Manager

node-two
Worker

node-three
Worker
```

This gives us a clear environment for learning the responsibilities of each role.

We'll be able to see:
- how a Swarm is created,
- how workers join,
- how services are created,
- how tasks are scheduled,
- how replicas are distributed,
- and how Swarm reacts when something changes.

Later, the same concepts can be extended to environments with multiple managers and stronger fault tolerance.

---

# What Did We Learn?

A **Docker Swarm** combines multiple Docker hosts into a cluster.

The important pieces are:

- **Nodes** are machines participating in the Swarm.
- **Managers** coordinate and maintain the cluster.
- **Workers** execute tasks assigned by managers.
- **Services** describe the workloads we want running.
- **Tasks** are the individual units scheduled onto nodes.
- **Desired state** describes what should be running.
- Swarm continually works to reconcile the actual state with that desired state.
- Multiple hosts allow workloads to be distributed.
- Multiple replicas can reduce dependence on one container or worker.
- Multiple managers can reduce dependence on one manager.

Most importantly:

> **Instead of manually managing individual containers on individual machines, we describe what we want the cluster to maintain.**

---

# What's Next?

Now that we understand **why** Docker Swarm exists and how its main components work, we're ready to turn those machines into an actual cluster.

We'll create:

```text
Docker Swarm
 node-one      Manager
 node-two      Worker
 node-three    Worker
```

Continue with: [[Docker - Creating The First Swarm]]


---

# Further Reading

- [Docker Docs - Swarm mode](https://docs.docker.com/engine/swarm/)
- [Docker Docs - Swarm mode key concepts](https://docs.docker.com/engine/swarm/key-concepts/)
- [Docker Docs - How nodes work](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/)
- [Docker Docs - How services work](https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/)
- [Docker Docs - Administer and maintain a Swarm](https://docs.docker.com/engine/swarm/admin_guide/)