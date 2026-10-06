---
icon: LiWholeWord
---
# Docker Swarm - Definitions

Docker Swarm introduces a lot of terminology that describes different parts of the cluster and how they work together.

Use this page as a quick reference whenever one of those terms appears.

---

## Cluster

A **cluster** is a group of machines working together as one system.

In Docker Swarm, the cluster consists of multiple Docker hosts called **nodes**.

```text
Swarm Cluster

node-one
node-two
node-three
node-four
```

---

## Node

A **node** is a machine participating in the Swarm.

In our lab, each Ubuntu Server VM is a node.

A node can have one of two roles:

- **Manager**
- **Worker**

Manager nodes can also run service tasks by default.

---

## Manager

A **manager** is a node responsible for coordinating and managing the Swarm.

Managers:

- Maintain the state of the Swarm.
- Schedule tasks onto nodes.
- Manage services.
- Respond to changes and failures.
- Participate in consensus with other managers.

A manager can also run containers for services by default.

> **Managers coordinate.**

---

## Worker

A **worker** is a node that executes tasks assigned to it by managers.

Workers run the containers that perform the actual workload and report the state of their tasks back to managers.

> **Workers execute.**

---

## Service

A **service** describes a workload that you want the Swarm to run and maintain.

For example:

```bash
docker service create \
  --name nginx-web \
  --replicas 3 \
  nginx
```

Here, `nginx-web` is the **service**.

The service describes things such as:

```text
Image:       nginx
Replicas:    3
Name:        nginx-web
```

Think of a service as:

> **"This is what I want the Swarm to keep running."**


---

## Task

A **task** is an instruction from the Swarm manager telling a node to run a container for a service.

Imagine we created an NGINX service with 3 replicas:

```text
Service: nginx-web
Replicas: 3
```

Swarm needs three NGINX containers running. To make that happen, the manager assigns work to nodes:

```text
Task 1: Run NGINX on node-one
Task 2: Run NGINX on node-two
Task 3: Run NGINX on node-three
```

Each node receives its task and starts the required container.

```text
Task 1 → NGINX container
Task 2 → NGINX container
Task 3 → NGINX container
```

So a **task connects a service to a container running on a particular node**.

If a container fails, that task does not simply become a new container somewhere else. Swarm can create a **new task** and assign it to an available node.

> **Service:** describes what Swarm should keep running.
> **Task:** tells a node to run one container for that service.
> **Container:** actually runs the application.

---

## Replica

A **replica** tells Swarm **how many copies of a service we want running at the same time**.

For example, if we configure: 
* x3 replicas 
* nginx 

we are saying:

> **"Keep 3 copies of `nginx-web` running."**

Swarm will then create the tasks needed to keep those 3 copies running.

If one stops working, Swarm still knows that we asked for **3 replicas**, so it will try to get back to 3 running copies.

```text
Wanted:  3 replicas
Running: 2

Swarm needs to start another one.
```

The number of replicas does not tell Swarm **which nodes** should run them. It only tells Swarm **how many should be running**.

> **Replicas = how many instances the service wants running.**


---

## State

**State** describes the current or intended condition of something.

In Swarm, we commonly compare two kinds of state:

```text
Desired state
Actual state
```

For example:

```text
Desired: 4 replicas
Actual:  3 replicas
```

There is currently a difference between what we **want** and what is actually **running**.

---

## Desired State

The **desired state** describes what we want the Swarm to look like.

For example:

```text
Service: nginx-web
Replicas: 4
Image: nginx:1.28
```

This tells Swarm that we want:

```text
4 running instances
using nginx:1.28
```

Swarm continually works toward maintaining that state.

---

## Actual State

The **actual state** describes what is currently happening in the cluster.

For example:

```text
Desired: 4 replicas
Actual:  3 replicas
```

Perhaps one node failed and only three tasks are currently running.

Swarm can compare this actual state with the desired state and respond.

---

## Reconciliation

**Reconciliation** is the process of comparing the desired state with the actual state and taking action when they don't match.

For example:

```text
Desired: 4
Actual:  3
```

The manager sees that one task is missing and schedules another.

```text
Desired: 4
Actual:  4
```

Now they match.

> **Observe actual state → compare with desired state → correct the difference.**

Swarm doesn't necessarily repair the failed container. It can create a **replacement task** instead.

---

## Scheduling

**Scheduling** is the process of deciding which eligible node should run a task.

For example, the manager might decide:

```text
Task 1    node-one
Task 2    node-two
Task 3    node-two
```

Scheduling does not automatically mean tasks will be distributed evenly across every node.

The **manager** is responsible for scheduling.

---

## Scaling

**Scaling** means changing how many replicas of a service Swarm should maintain.

For example:

```text
3 replicas → 4 replicas
```

changes the desired state.

Swarm then creates another task to reach the new desired number.

> **Scaling changes how many instances should run.**

---

## Rolling Update

A **rolling update** gradually replaces existing tasks with tasks using a new service configuration.

For example:

```text
nginx:latest
      ↓
nginx:1.28
```

Rather than changing the image inside existing containers, Swarm creates replacement tasks using the new image.

> **Scaling changes how many instances should run.**
>
> **Rolling updates change what version those instances should run.**

---

## Operational Model

An **operational model** describes the general way a system is managed.

For Swarm, the important operational model is:

> **Declarative**

This means we describe the result we want rather than manually specifying every action required to produce it.

---

## Declarative

**Declarative** means describing **what state you want**, while the system determines how to reach and maintain that state.

For example, we say:

```text
I want 4 replicas.
```

We don't manually say:

```text
Create container A on node-one.
Create container B on node-two.
Create container C on node-three.
Create container D on node-four.
```

Swarm determines the necessary actions itself.

This is why **desired state and reconciliation** are central to Swarm.

---

## Imperative

**Imperative** means telling a system the individual actions it should perform.

Conceptually:

```text
Do this.
Then do this.
Then do this.
```

Declarative is instead:

```text
This is the result I want.
```

Docker Swarm's service management follows a **declarative operational model**.

---

## Routing Mesh

The **routing mesh** allows traffic sent to a service's published port on any Swarm node to reach a task for that service.

Imagine NGINX is running on `node-three`.

A request can still enter through:

```text
node-one:80
```

and Swarm can route the connection:

```text
Request → node-one:80 → routing mesh → NGINX task on node-three
```

The node receiving the connection therefore does **not** have to be the node running the container that handles it.

The routing mesh operates at **Layer 4**, dealing with TCP/UDP connections rather than Layer 7 HTTP routing.

---

## Published Port

A **published port** makes a service accessible through the Swarm.

For example:

```bash
--publish 80:80
```

publishes port `80` for the service.

Together with the routing mesh, that published port can be reached through Swarm nodes even when the receiving node isn't running a task for that service.

---

## Consensus

**Consensus** means the Swarm managers agreeing on the state and management decisions of the cluster.

When multiple managers exist, they need a consistent view of things such as:

```text
Which nodes belong to the Swarm?
Which services exist?
What configuration should those services have?
```

Docker Swarm uses the **Raft consensus algorithm** for this.

> **Consensus is about the managers agreeing on the state of the cluster.**

---

## Raft

**Raft** is the consensus algorithm used by Docker Swarm managers.

It allows multiple managers to maintain a consistent view of the Swarm's management state.

One manager acts as the:

```text
Leader
```

while other available managers can be:

```text
Reachable
```

A majority of managers is required for the manager group to continue making decisions.

---

## Quorum

**Quorum** means having the required **majority of managers** available for consensus.

For example:

```text
Managers    Majority required
1           1
3           2
5           3
7           4
```

With three managers:

```text
3 managers
2 required for majority
```

one manager can become unavailable while the other two still form a majority.

This is why Swarm commonly uses an **odd number of managers**.

---

## Leader

The **Leader** is the manager currently coordinating decisions among the Swarm managers.

With multiple managers, you might see:

```text
node-one     Leader
node-five    Reachable
```

There is normally one Leader at a time.

If the Leader becomes unavailable and the remaining managers still have quorum, another manager can become Leader.

---

## Reachable

**Reachable** indicates that a manager is available and participating in the manager group but is not currently the Leader.

For example:

```text
node-one     Leader
node-five    Reachable
```

Both are managers.

`Leader` and `Reachable` describe their current status within the manager group.

---

## Join Token

A **join token** is a secret credential used to authorize a new node joining the Swarm.

Swarm has separate tokens for:

```text
Worker token
Manager token
```

They can be retrieved from a manager with:

```bash
docker swarm join-token worker
```

or:

```bash
docker swarm join-token manager
```

A node uses the appropriate token when joining the Swarm.

Join tokens should be treated as **secrets**.

---

# Quick Reference

| Term                  | Simple meaning                                      |
| --------------------- | --------------------------------------------------- |
| **Cluster**           | Multiple machines working together                  |
| **Node**              | A machine participating in the Swarm                |
| **Manager**           | Coordinates and manages the Swarm                   |
| **Worker**            | Runs tasks assigned by managers                     |
| **Service**           | Description of a workload Swarm should maintain     |
| **Task**              | One unit of a service assigned to a node            |
| **Replica**           | One desired running instance of a service           |
| **Desired State**     | What we want the cluster/service to look like       |
| **Actual State**      | What is currently happening                         |
| **Reconciliation**    | Making actual state match desired state             |
| **Scheduling**        | Deciding which node should run a task               |
| **Scaling**           | Changing the desired number of replicas             |
| **Rolling Update**    | Gradually replacing tasks with a new configuration  |
| **Operational Model** | The general way a system is managed                 |
| **Declarative**       | Describe what you want; the system determines how   |
| **Routing Mesh**      | Routes published service traffic to available tasks |
| **Published Port**    | Port through which a service is exposed             |
| **Consensus**         | Managers agreeing on the cluster's management state |
| **Raft**              | Consensus algorithm used by Swarm managers          |
| **Quorum**            | Majority of managers required for consensus         |
| **Leader**            | Manager currently coordinating manager decisions    |
| **Reachable**         | Available manager that isn't currently Leader       |
| **Join Token**        | Secret credential allowing a node to join the Swarm |