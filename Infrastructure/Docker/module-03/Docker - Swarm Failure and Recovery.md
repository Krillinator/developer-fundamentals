---
icon: LiCross
---
# Overview

![[undraw_server-failure_syqp.svg|285]]

Until now, every change to our service has been intentional.

When we scaled replicas from 1 to 3, we changed the **desired state**, and Swarm created additional tasks to reach it.

When we performed a rolling update, we changed which version we wanted running, and Swarm replaced the old tasks with new ones.

But what happens when **we don't change the desired state at all**?

Imagine our service is happily running with three replicas when one of our servers suddenly goes offline.

Swarm still wants:

```text
Desired state: 3 replicas
```

but the cluster may now only have:

```text
Actual state: 2 replicas
```

Something simply went wrong. What does Swarm do then?
**Let's break our cluster and find out.**

---

# Check Our Service Before the Failure

Before we break anything, let's see where our tasks are currently running:

```bash
docker service ps nginx-web
```

Choose a **worker node that currently has at least one task running on it**.
For our example, we'll imagine that `node-three` is running one of the tasks.

---

# Watch the Service

Instead of repeatedly typing:

```bash
docker service ps nginx-web
```

Linux provides a useful command called:

```text
watch
```

On `node-one`, run:

```bash
watch -n 1 docker service ps nginx-web
```

This runs:

```bash
docker service ps nginx-web
```

again every **1 second**.

Keep this terminal open.

> [!TIP]
> Press `Ctrl+C` when you want to stop `watch`.

Now we'll deliberately cause a failure from another terminal.

---

# What Do We Think Will Happen?

Before we break anything, think about what Swarm currently knows.

The service says:

```text
Desired state: 3 replicas
```

and currently:

```text
Actual state: 3 replicas
```

Now imagine `node-three` disappears and takes one NGINX container with it.

Our desired state hasn't changed:

```text
Desired state: 3 replicas
```

But our actual state has:

```text
Actual state: 2 replicas
```

Swarm now has a problem.

If everything we've learned about reconciliation is true, the manager should detect that the service no longer has enough running tasks and work to restore the desired state.

Let's see if it does.

---

# Remove a Worker

Go to the terminal for the worker we selected, for example `node-three`.

Run:

```bash
docker swarm leave
```

The node leaves the Swarm.

Any Swarm tasks that were running on that node are no longer available to the cluster.

Now return to the terminal where we're watching:

```bash
watch -n 1 docker service ps nginx-web
```

Watch what happens.

---

# Swarm Reconciles the Service

The manager still has the same desired state:

```text
3 replicas
```

It doesn't interpret `node-three` leaving as:

> "I guess we only want two replicas now."

The desired state hasn't changed.

Instead, the manager eventually sees that one of the required tasks is no longer available and schedules replacement work onto an eligible node.

![[docker-swarm-docker-swarm-leave-reconcilliation-terminal-results.png]]

This is **reconciliation**.
Notice how node-three has been completely shutdown, yet node-one is running nginx-web.3

> **Swarm continuously works to make the actual state of the service match its desired state.**

---

# Did Swarm Repair the Container?

Not exactly.
The container that disappeared with `node-three` isn't somehow transported to another machine or repaired.

Instead, Swarm knows what the **service** is supposed to look like.

It knows:

```text
nginx-web
Desired replicas: 3
```

So when one task is lost, the manager can schedule a **replacement task** on another eligible node, which results in another container being started there.

That's why we manage a **service** rather than depending on individual containers.
The individual container is disposable.
The desired state of the service is what Swarm tries to preserve.


---

# But What If the Manager Fails?

There's one more interesting question.

We deliberately removed a **worker**.

What if we shut down:

```text
node-one
```

instead?

In our lab, `node-one` is our **only manager**.

The containers already running on the workers don't suddenly disappear just because the manager goes offline. However, we've lost the manager responsible for coordinating changes to the Swarm.

That means our current lab has a **single point of failure in the management layer**.

A production Swarm that requires manager high availability can use multiple manager nodes. Those managers maintain the Swarm state using consensus, allowing the cluster to tolerate some manager failures while a quorum remains available.

We intentionally didn't build that architecture here.

Our topology:

```text
1 Manager
2 Workers
```

was designed to make the responsibilities easy to see while learning.

> [!NOTE]
> **Application availability and manager availability are different problems.**
>
> Multiple service replicas can protect us from losing an individual application instance, while multiple managers are used when we need the Swarm's management layer itself to remain highly available.

> [!IMPORTANT]
> When a failed worker rejoins the Swarm, Docker does **not automatically move existing tasks back to it**.
>
> If the service already has its desired number of running replicas, the desired and actual states match, so there is nothing to reconcile.
>
> Swarm maintains the **desired state** of the service; it does not continuously rebalance already-running tasks simply because another node becomes available.

---

# What Did We Learn?

We've now seen the complete desired-state loop.

We first told Swarm what we wanted:

```text
nginx-web
3 replicas
```

Then we deliberately caused the actual state to change by removing a worker.

We didn't create another container ourselves. We didn't tell Swarm which remaining node should receive the replacement.

The manager detected that the service no longer matched its desired state and scheduled replacement work.

This is the core pattern we've been building toward throughout this module:

> **Declare the desired state → observe the actual state → reconcile the difference.**

Scaling, failure recovery and rolling updates may look like different features, but they're all built around the same fundamental idea: **we describe what we want the cluster to look like, and the orchestrator works to make and keep it that way.**

Continue on: [[Docker - Hands-On Docker Swarm Practice 07]]