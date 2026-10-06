---
icon: LiWorkflow
---
# Overview

![[docker-banner.avif]]

So far, we've learned how to build images and run containerized applications using Docker on our **local machine**.

But running a few containers on one machine is very different from running applications across **multiple machines**.
	Imagine our application has grown and one machine is no longer enough.

This is where **container orchestration** comes in.

---

# Container Orchestration

Container orchestration helps us manage containerized applications across multiple machines.
Some of the problems orchestration can help us solve include:
- **Scheduling** workloads across machines
- **High availability**
- **Reconciliation**
- **Scaling**
- **Managing distributed workloads**

There are several orchestration solutions available.
One of the most widely used is **Kubernetes**.

But before moving on to Kubernetes, we're going to explore these concepts using **Docker Swarm**.

---

# Docker Swarm

**Docker Swarm** is an orchestration feature built into Docker Engine.

Instead of managing containers individually on a single machine, Docker Swarm allows multiple machines running Docker to work together as a **cluster**.

Each machine participating in that cluster is called a: **Node**
A node can be a physical computer, cloud server or virtual machine.

![[node-explained-visually.png|487]]

For this module, our nodes will be **virtual machines** running Ubuntu Server.
This gives us a small environment where we can explore the fundamentals of container orchestration before eventually moving on to Kubernetes.

> **So far, we've managed containers on one machine. Now we're going to manage containers across multiple machines.**

---

# Creating Our Machines

To properly explore Docker Swarm, we need more than one Docker host.
We could use several physical computers or cloud servers, but that would add unnecessary complexity to our learning environment.

Instead, we'll create several **virtual machines** on our own computer.
Each VM will run:

```text
Ubuntu Server
+
Docker Engine
```

Once they're connected together through Docker Swarm, each VM will become a **node in our cluster**.

Before continuing with Docker Swarm, we'll therefore prepare the machines that our cluster will run on.

Therefore a Hypervisor and a Ubuntu Server is required.

Follow: 
1. [[VM - Start Here]]
2. [[VM - Ubuntu Server for Docker Swarm]]

This guide covers downloading the correct Ubuntu Server image for your computer and preparing the virtual machines we'll use as our Docker hosts.

---

# Prerequisites

Before continuing:
- Be familiar with the Docker commands used in the previous modules
- Have a supported hypervisor installed: [[VM - Start Here]]
- Have a supported Ubuntu Server for your hypervisor: [[VM - Ubuntu Server for Docker Swarm]]

---

# What Did We Learn?

We now understand why orchestration becomes useful.
Running containers across multiple machines introduces problems that don't appear when we're simply running containers on our own computer.
Container orchestration also help coordinate those machines and the workloads running across them.

We also introduced an important term:

> **Node = a machine participating in a cluster**

For our Docker Swarm, those nodes will be **Ubuntu Server virtual machines running Docker Engine**.

---

# What's Next?

We know **why** orchestration is useful, and we have the machines that will become our nodes.
Now let's set up an environment if you haven't already: [[VM - Ubuntu Server for Docker Swarm]]

Once that's finished, move on to [[Docker - Preparing Docker Swarm Hosts]]