---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

Our Swarm has grown from a few Docker hosts into a cluster with multiple workers and services running across them.

But there is still something important about our architecture:
**`node-one` is our only manager.**

So far, every management decision in our Swarm has depended on that single machine.

In this lab, we'll prepare another VM and join it to the Swarm, but this time we won't add it as a worker.

We'll add it as a **manager**.

> **The goal is simple: add a second manager to our existing Docker Swarm and explore how manager nodes work together.**

---

# 1. Prepare Another VM

Create another VM using the same process we've used previously.

You don't need new instructions for this part. You should already know how to:

- Create or clone the VM.
- Give it a unique hostname.
- Find and record its IP address.
- Verify that Docker is installed and working.

For this lab, name the machine:

```text
node-five
```

Once `node-five` is ready, return to `node-one`.

---

# 2. Check Our Current Managers

On `node-one`, inspect the Swarm:

```bash
docker node ls
```

Look at the `MANAGER STATUS` column.

`node-one` should currently be the only manager and should show:

```text
Leader
```

The other nodes are workers, so they won't have a manager status.

Our Swarm currently has a single machine responsible for coordinating the cluster.

---

# 3. Get the Manager Join Command

Previously, when adding workers, we used:

```bash
docker swarm join-token worker
```

This time we want the new machine to become a **manager**, so we need a different join token.

On `node-one`, run:

```bash
docker swarm join-token manager
```

Docker will provide a command similar to:

```bash
docker swarm join \
  --token SWMTKN-1-... \
  <manager-IP>:2377
```

> [!IMPORTANT]
> Manager and worker join tokens are different.
>
> A machine joining with the **worker token** becomes a worker. A machine joining with the **manager token** becomes a manager.

Treat both tokens as secrets and don't store the real values in your notes or Git repository.

---

# 4. Add `node-five` as a Manager

Move over to `node-five` and run the manager join command provided by `node-one`.

Once it has joined, return to `node-one` and check:

```bash
docker node ls
```

You should now have two manager nodes.

Look closely at the `MANAGER STATUS` column.

You should see something similar to:

```text
node-one     Leader
node-five    Reachable
```

`node-one` remains the **Leader**, while `node-five` is another manager participating in the management of the Swarm.

---

# 5. Leader vs Reachable

Adding another manager doesn't mean that both managers independently make conflicting decisions.

Docker Swarm managers work together using **Raft consensus**.

One manager acts as the:

```text
Leader
```

while the other manager can be:

```text
Reachable
```

The leader coordinates changes to the Swarm's state, while the manager nodes maintain a consistent view of that state.

If leadership needs to change, another eligible manager can become the leader.

> **Managers don't operate as separate Swarms. Together, they form the control plane for the same Swarm.**

---

# 6. Managers Can Still Run Tasks

Now inspect our existing service:

```bash
docker service ps nginx-web
```

Remember that being a manager describes a node's **responsibility in the Swarm**. It does not mean that the machine is prohibited from running application containers.

By default, manager nodes are also eligible to receive tasks.

That means `node-five` is now both:

```text
Manager
+
Eligible to run service tasks
```

If we wanted a manager to perform only management duties, we could change its availability to `Drain`, but we won't do that in this lab.

---

# 7. Do We Have High Availability Yet?

We now have:

```text
2 managers
```

This gives us another manager, but there is an important limitation.

Docker Swarm managers use **majority consensus** to make decisions. With two managers, a majority requires both managers to be available.

So although we've introduced another manager, **two managers isn't an ideal highly available manager configuration**.

This is why Swarm manager groups are normally built using an **odd number of managers**.

For example:

```text
1 manager
3 managers
5 managers
```

With three managers, the Swarm can lose one manager and the remaining two still form a majority.

> [!IMPORTANT]
> Adding a second manager teaches us how multiple managers cooperate, but **three managers is the more useful configuration when the goal is manager fault tolerance**.

---

# What Did We Practice?

Previously, we expanded our Swarm by adding another **worker**. This increased the number of machines available to run our workloads.

This time, we expanded the **management side** of the Swarm.

We prepared `node-five`, retrieved the manager join token and joined the machine as another manager. We also saw that managers have different roles within the manager group: one acts as the `Leader`, while other healthy managers can appear as `Reachable`.

Most importantly, we've introduced another part of orchestration:

> **Workers provide machines on which tasks can run. Managers coordinate and maintain the state of the Swarm.**

A Swarm can contain multiple of both.

---

# What's Next?

We've now expanded our Swarm with both **worker nodes and manager nodes**, and we've seen how their responsibilities differ within the cluster.

Before moving on, it's time to check how well these concepts have stuck.

In the next module, you'll complete a **theoretical knowledge check** covering the Docker Swarm concepts we've explored, including:
- Nodes and their roles.
- Managers and workers.
- Services, tasks and replicas.
- Desired state and reconciliation.
- Scaling and rolling updates.
- Failure and recovery.
- Multiple managers and consensus.

The goal isn't to memorize every Docker command. It's to make sure you understand **what Swarm is doing and why**.

Continue with: [[Docker - Knowledge Check 03]]