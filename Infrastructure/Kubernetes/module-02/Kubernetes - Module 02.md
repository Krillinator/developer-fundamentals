---
icon: LiWorkflow
---
# Overview

![[kubernetes-banner.jpg]]

In the previous Docker modules, we learned how to build and run containers. We also saw that running a few containers ourselves is relatively straightforward.

> **How do we keep a large container environment running without managing every container ourselves?**

In this module, we'll explore **Kubernetes**, the most widely used container orchestration platform.

We'll start by understanding what Kubernetes is and why orchestration is needed. Then we'll look inside a Kubernetes cluster and learn how responsibilities are divided between the **Control Plane** and **Worker Nodes**.

From there, we'll work with some of the core Kubernetes objects:
- **Pods**
- **ReplicaSets**
- **Deployments**

We'll also introduce **kubectl**, the command-line tool used to communicate with a Kubernetes cluster, and see how Kubernetes resources can be created using both **commands** and **YAML files**.

Finally, we'll put everything together by using `kubectl` to create and manage resources inside a Kubernetes cluster.

By the end, the goal isn't just to know a collection of Kubernetes commands. It's to understand **what Kubernetes is managing for us and why**.


---

# Goals

![[undraw_target_d6hf.svg|383]]

After completing this module, you should be able to:
- Explain **container orchestration** and why it's useful
- Explain what **Kubernetes** is and what problems it solves
- Describe the basic architecture of a **Kubernetes cluster**
- Explain the roles of the **Control Plane** and **Worker Nodes**
- Identify the key components running on the Control Plane and Worker Nodes
- Explain how **Pods, ReplicaSets and Deployments** work
- Explain what **kubectl** is and how it communicates with a Kubernetes cluster
- Use basic `kubectl` commands to inspect and manage Kubernetes resources
- Understand the difference between **imperative and declarative** approaches
- Create and manage Kubernetes resources using `kubectl`


---

## Kubernetes Is Flexible

Kubernetes focuses on **running and managing containerized applications**, rather than providing everything needed to develop an application.

It does **not** prescribe:
- Programming languages or frameworks
- CI/CD pipelines
- Logging solutions
- Monitoring and alerting tools

These can instead be chosen and integrated separately.

> **If an application can run in a container, Kubernetes can manage it.**

This flexibility, combined with its open-source ecosystem and automation capabilities, has made Kubernetes the **de facto standard for container orchestration**.


---

# What Kubernetes Is NOT

Kubernetes manages **containerized applications**, but it does not provide every tool needed to build and operate an application. It is **not an all-in-one Platform-as-a-Service (PaaS)**.

For example, Kubernetes does not:
- Build your application source code
- Provide a built-in CI/CD pipeline
- Decide which programming language or framework you use
- Require a specific logging solution
- Require a specific monitoring or alerting solution

Instead, Kubernetes is designed to be **flexible**.
This is great because we choose the tools we want and integrate them with Kubernetes.

Kubernetes primarily enters the picture once we have **containerized workloads that need to be deployed and managed**.

> **Kubernetes doesn't build your application. It orchestrates the containers that run it.**

This separation is important because Kubernetes doesn't lock us into one particular development workflow or ecosystem.

---

## From Physical Servers to Containers

![[Container_Evolution.svg|656]]
*Figure from: https://kubernetes.io/docs/concepts/overview/*

Application deployment has evolved significantly over time. Applications were originally run directly on **physical servers**, which made resource allocation difficult and often resulted in expensive, underutilized hardware.

**Virtual Machines (VMs)** improved this by allowing multiple isolated machines to run on the same physical server. We've talked about VMs before, and they're great for isolation and resource management, but each VM is still an **entire machine with its own operating system**, which adds overhead.

**Containers** take this a step further. Instead of running a separate operating system for every application, containers share the host OS while keeping applications isolated. This makes them much more **lightweight, portable and efficient**, allowing many applications to run consistently across different environments.

Containers made deploying applications much easier, but as the number of containers and machines grows, **managing them becomes a new challenge**.

That's where **container orchestration** with tools such as Kubernetes comes in!

---

Continue on: [[Kubernetes - Kubernetes Components (theory)]]