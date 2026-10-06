---
icon: LiBook
---
## Table of Contents
⬡ = component

- [[#Kubernetes Cluster|Kubernetes Cluster]]
- [[#Control Plane|Control Plane]]
    ⬡ [[#API Server|API Server]]
    ⬡ [[#etcd|etcd]]
    ⬡ [[#Scheduler|Scheduler]]
    ⬡ [[#Controller Manager|Controller Manager]]
    ⬡ [[#Cloud Controller Manager|Cloud Controller Manager]]
	[[#Meet the Pod|Meet The Pod]]
	[[#Pods|Pods]]
- [[#Worker Nodes|Worker Nodes]]
    ⬡ [[#kubelet|kubelet]]
    ⬡ [[#Container Runtime|Container Runtime]]
    ⬡ [[#kube-proxy|kube-proxy]]
- [[#Putting It Together|Putting It Together]]

---
# Overview

![[undraw_software-engineer_ljie.svg|380]]

A Kubernetes environment contains several components that work together to manage our applications.

*But what does that actually mean..?*

If we give Kubernetes an application to run..
* ***who decides where it should run?**
* who actually **starts the containers**?
* who notices and does 'something' if this 'something' stops working?

After all, Kubernetes needs a way to coordinate all of this. Instead of having one program do **everything**, Kubernetes divides these responsibilities between several components that work together.

This may sound familiar from our Docker Swarm setup, and there are some similarities. However, Kubernetes is also architecturally and structurally different.

**Let's explore.**

---

## Kubernetes Cluster

Our goal is to be able to fully identify each Kubernetes Component within this diagram:

![[kubernetes-components-diagram-overview.png]]

**So.. the Cluster, what is it?**
A deployment of Kubernetes is called a **cluster** or **kubernetes cluster**. 
Note how it encompasses all the components within as well as `two sides`.

At a high level, a Kubernetes cluster consists of two sides:
1. The **Control Plane** makes decisions about the cluster and monitors what is happening.
2. The **Worker Nodes** are the machines where our containerized applications actually run.

![[kubernetes-cluster-control-plane-nodes-docker-swarm-simplification-diagram.png]]
*When we talk about a **Node**, think of a machine that Kubernetes can use.*

This is our **high-level view** of a Kubernetes cluster.	
For now, think of the **Control Plane** as the part that manages and coordinates the cluster, while the **Worker Nodes** provide the machines where our workloads run.

**The diagram is intentionally simplified.** 
The Control Plane doesn't simply send containers directly to Nodes, and Nodes contain more than just our application containers.

We'll build on this diagram step by step, **zooming into each part** to see what's actually happening underneath.

> **Control Plane manages. Worker Nodes run the workloads.**

---

# Control Plane

The **Control Plane** is responsible for managing the Kubernetes cluster.

It makes decisions such as:
- What should be running?
- Where should it run?
- Does the current state match the desired state?
- Does something need to be created, replaced or changed?

Sounds like a lot, doesn't it? 
That's why we call it the **Control Plane**.

![[kubernetes-cluster-control-plane-components-diagram.png]]

The Control Plane itself consists of several components:
 1. API Server
 2. etcd
 3. Scheduler
 4. Controller Manager
 5. Cloud Controller Manager

Let's look at what each one does.

---

## Meet the Pod

![[Infrastructure/Kubernetes/res/svg/resources/labeled/pod.svg|150]]

Before we continue with our components, we need to meet something slightly different: the **Pod**.

A Pod isn't a Kubernetes component. It's a **Kubernetes Object**, and it's something many of these components work together to manage.

> [!NOTE] So why are we covering Pods here?
> Many of the components we're about to explore **work with Pods**, so we need to understand what a Pod is first.
>
> We'll explore Kubernetes Objects properly in the next module.

---
# Pods

**Not a component, but something our components manage**
We now know that a Kubernetes cluster contains **Nodes** where our workloads run.

![[kubernetes-cluster-nodes-and-pods.png]]

But there's one layer we've been hiding.

Kubernetes doesn't manage our application containers directly. Instead, it manages something called a **Pod**.

Let's zoom into one of our Nodes:

![[kubernetes-cluster-nodes-and-pods-2.png]]

A **Pod** is the smallest unit Kubernetes can create and manage. Our application containers run **inside Pods**, and those Pods run on our **Nodes**, where a component called the **kubelet** makes sure they are running as expected.

Most Pods contain a single application container, but a Pod can also contain multiple containers when they need to work closely together.

> **Node - Pod - Container**

So when we later ask Kubernetes to run an application, we'll be working with **Pods**, not directly with individual containers.

---
## API Server

![[api.svg|150]]

**Where every conversation with Kubernetes begins.**
For example, imagine we want to ask:

> **"What Pods are currently running?"**

We don't go to each Node and check ourselves. Instead, we send our request to the **API Server**.
Sounds odd right? API, HTTP protocol, and requests?

**Well..**
The **API Server** is the main entry point for communicating with a Kubernetes cluster. 
And just like ordinary API's, it has endpoints that we can send requests too.

![[kubernetes-cluster-control-plane-components-diagram.png]]

**So how does it work?**
Later, we'll use a tool called `kubectl` to do something like:

```bash
kubectl get pods
```

Behind the scenes, our request is sent to the API Server that handles our request.

The same idea applies when we want to **change** something. 
Requests to:
* create, 
* update or 
* delete Kubernetes resources 

also go through the API Server.
So don't worry too much about the terminologies here.

> **Think of the API Server as the front door to Kubernetes.**

Other Kubernetes components also use this API to interact with the cluster.

> [!info]- Familiar with HTTP and APIs?
> `kube-apiserver` is an actual server that exposes the **Kubernetes HTTP API**.
>
> Just like a web API, it listens for HTTP(S) requests and provides endpoints for different Kubernetes resources.
>
> ```text
> Client                    Server
>
> kubectl ─ HTTP(S) - kube-apiserver
> ```
>
> So `kubectl` is essentially a **client of the Kubernetes API**.
>
> HTTP does not mean public Internet. This communication can happen entirely within a private network.
---

Read more: https://kubernetes.io/docs/concepts/architecture/#kube-apiserver

---
# etcd

![[Infrastructure/Kubernetes/res/svg/infrastructure_components/labeled/etcd.svg|150]]

**The memory of our Kubernetes cluster**
The name **etcd** comes from two ideas:  
- `/etc` - the directory traditionally used for configuration files on Unix/Linux systems  
- `d` - **distributed**  
  
So you can roughly think of **etcd** as configuration/state storage that can be distributed across multiple machines
  
**But why does Kubernetes need it?**
We now know that the **API Server** gives us a way to interact with Kubernetes.

However, ask yourself this:

> **How does Kubernetes know what exists in the cluster?**

Kubernetes needs somewhere to store its cluster data. And we know that Kubernetes consist of separated components that interact with one another.
That's where **etcd** comes in.

**etcd** is a key-value store that holds the data Kubernetes uses to represent the state of the cluster.

![[kubernetes-cluster-etcd-relationship-with-api.png]]

For example, Kubernetes needs to keep track of things such as:
- What Pods exist
- What Nodes exist
- What applications we've asked it to run
- How those resources are configured

> **Think of etcd as Kubernetes' source of truth, It's a database that stores information about the cluster**

Read more: https://kubernetes.io/docs/concepts/architecture/#etcd

---

## Scheduler

![[sched.svg|150]]

**Choosing the right Node for our Pods**
When Kubernetes needs to run a new **Pod**, it needs to decide which Worker Node should run it.
That's the job of the **Scheduler**.

Imagine we have two Worker Nodes:

![[kubernetes-cluster-scheduler-pod-node-relationship-diagram.png]]

It looks for Pods that **haven't been assigned to a Node yet**, evaluates the available Nodes, and selects a suitable one.  
  
When making this decision, it considers factors such as:  
- Available resources, such as CPU and memory  
- The Pod's resource requirements  
- Hardware, software and policy constraints  
- Where other related Pods are running

> **The Scheduler answers: "Where should this Pod run?"**

The Scheduler decides where the workload should go. It does not actually run the container itself.

Read more: https://kubernetes.io/docs/concepts/architecture/#kube-scheduler

---

## Controller Manager

![[c-m.svg|150]]

**Making sure reality matches what we asked for.**
The **Controller Manager** runs controllers that continuously watch the state of the cluster.

They compare the **desired state** with the **actual state** and take action when they don't match.

For example, if we want **2 Pods** running but only **1 Pods** exist, a controller can work to bring the cluster back toward the desired state.

![[kubernetes-controller-manager-missing-pod-diagram.png]]

> [!info]- Why is it called the Controller **Manager**?
> Kubernetes actually has **many different controllers**, each responsible for watching a particular part of the cluster.
>
> Logically, these are separate controllers, but Kubernetes packages them together and runs them within the **kube-controller-manager** process.
>
> Some examples include:
>
> - **Node Controller** - notices and responds when Nodes go down.
> - **Job Controller** - creates Pods for one-off tasks and watches them until completion.
> - **EndpointSlice Controller** - helps maintain the connection between Services and their Pods.
> - **ServiceAccount Controller** - creates default ServiceAccounts for new Namespaces.
>
> These are only a few examples. Kubernetes contains many different controllers.

Read more: https://kubernetes.io/docs/concepts/architecture/#kube-controller-manager

---

## Cloud Controller Manager

![[c-c-m.svg|150]]

**Connecting Kubernetes with the cloud around it.**
So far, we've focused on the components that manage the **Kubernetes cluster itself**.
But our cluster doesn't necessarily exist on our local computer.

In production, a Kubernetes cluster may be running within a cloud environment such as **AWS, Azure or Google Cloud**.

![[kubernetes-cloud-controller-manager-diagram.png]]

Even though the cluster is running in the cloud, Kubernetes and the cloud provider are still **separate systems**.

Sometimes Kubernetes needs to interact with infrastructure provided by that cloud platform.

For example, Kubernetes may need to:
- Check whether a Node still exists in the cloud
- Configure routes in the cloud network
- Create or manage a cloud load balancer

This is where the **Cloud Controller Manager (`c-c-m`)** comes in.
It runs **cloud-specific controllers** that communicate with the cloud provider through its API.

For example, a cloud-specific **Service Controller** can cause a cloud load balancer to be created or updated.

That load balancer exists as **cloud infrastructure outside the Kubernetes cluster**, even though the cluster itself may be running within the same cloud environment.

> **Think of the Cloud Controller Manager as the part of Kubernetes that understands how to work with the cloud provider hosting it.**

Read more: https://kubernetes.io/docs/concepts/architecture/#cloud-controller-manager


---

# Worker Nodes

![[Infrastructure/Kubernetes/res/svg/infrastructure_components/labeled/node.svg|150]]

**Where our Pods actually run.**
The Control Plane manages the cluster, but what about our applications, where do they exist? 

That's where **Worker Nodes** come in.

![[kubernetes-cluster-nodes-and-pods.png]]

A Worker Node is a machine responsible for running application workloads. A Kubernetes cluster can contain multiple Worker Nodes.

A Node can be a **physical machine or a virtual machine (VM)**.

In cloud environments, Worker Nodes are commonly cloud instances such as **Amazon EC2, Azure Virtual Machines or Google Compute Engine instances**. In local or on-premises environments, they can instead run on local VMs or physical servers.

This should already feel familiar from **Docker Swarm**.

In [Docker - Module 03](<Docker - Module 03>), we learned that a Swarm is made up of multiple machines, where each participating machine becomes a **Node** in the cluster.

![[node-summary-diagram-overview.png]]

The same general idea applies to Kubernetes:

> **A Node is a machine participating in the cluster.**

That machine might be physical, virtual or hosted in the cloud. Regardless of where it runs, Kubernetes treats it as a **Node in the cluster**. And that's kind of nice, because **we already know this concept**. Docker Swarm and Kubernetes are different technologies, but the idea of grouping multiple machines into a cluster and calling each participating machine a **Node** is something we've already worked with. 
We're not starting from scratch here; we're building on what we already know.

[[Docker - Understanding Docker Swarm (theory)]]

Where Kubernetes starts to add its own pieces is in **what runs on those Nodes**. 
Each Node runs a set of Kubernetes components that allow it to participate in the cluster, maintain the workloads assigned to it and provide the environment needed to run them.  

![[kubernetes-components-diagram-overview.png]]

Let's take a closer look at those components.


---

# kubelet

![[kubelet.svg|150]]

**Making sure our pods are running as expected**
Let's start with one of the most important components running on each Node: the **kubelet**.

![[kubernetes-cluster-nodes-and-pods.png]]

The **kubelet** is a program that runs on every Node in the cluster.

It acts as Kubernetes' **agent on that Node**, receiving instructions about which Pods should be running there and making sure the containers inside those Pods are actually **running and healthy**.

In other words, Kubernetes decides **what should be running**, while the kubelet makes sure it **actually runs on its Node**.

![[kubernetes-node-agent-kublet-diagram.png]]

This gives the kubelet a very specific responsibility: 
	it looks after the workloads **on its own Node**. 
If Kubernetes expects a particular Pod to be running there, the kubelet works to make sure its containers match that expectation.

It only manages containers that are part of Pods created through Kubernetes. Other containers that happen to be running on the machine are outside of its responsibility.

> **Think of the kubelet as Kubernetes' agent on each Node, making sure its assigned Pods are actually running as expected.**

Read more: https://kubernetes.io/docs/concepts/architecture/#kubelet

---

## Container Runtime

**The engine that actually runs our containers.**

![[kubernetes-node-container-runtime-component-diagram.png]]

The **kubelet** knows which containers are supposed to be running on its Node, but it doesn't actually create or run those containers itself.  
  
For that, every Node needs software capable of **running containers**. This software is called a **container runtime**.  
  
Common container runtimes used with Kubernetes include **containerd** and **CRI-O**.  
  
The container runtime is an actual **program running on the Node**. When a container needs to run, it handles the work required to make that happen:  
- Pulls the required container image if it isn't already available  
- Creates a container from that image  
- Starts the container in an isolated environment  
- Manages the container throughout its lifecycle, such as stopping it and reporting its status  

So there is an important separation of responsibility:  
  
> **kubelet makes sure the correct containers should be running on its Node.**  
> **The container runtime actually creates and runs those containers.**

---

# kube-proxy

![[k-proxy.svg|150]]

**Helping Network traffic find the right Pods**
Applications running across different Nodes need a way to communicate over the network.

**kube-proxy** is a networking component that runs on Nodes and helps implement Kubernetes **Service networking**, directing network traffic toward the appropriate Pods.

For example, imagine our application has a **frontend** and a **backend** running in different Pods.

The frontend needs to send a request to the backend, but it doesn't need to know the exact IP address of the backend Pod. Instead, it sends the request to a **Service** representing the backend.

![[kubernetes-node-pod-cross-communication-problem-diagram.png]]

**kube-proxy** helps implement the networking rules that allow this traffic to reach one of the appropriate backend Pods.

The frontend doesn't need to know exactly which Pod is currently running the backend. It can send its request to the **Service**, and Kubernetes networking directs that traffic toward an appropriate backend Pod.

![[kubernetes-node-pod-cross-communication-proxy-solution-diagram.png]]


---


# Putting It Together

We can now see the basic structure of a Kubernetes cluster:

![[kubernetes-components-diagram-overview.png]]

A **Kubernetes Cluster** is made up of a **Control Plane** and one or more **Nodes**. 

The **API Server** acts as the central communication point, while **etcd** stores the cluster's state. 

The **Scheduler** decides where new Pods should run, the **Controller Manager (c-m)** keeps the actual state aligned with the desired state, and the **Cloud Controller Manager (c-c-m)** connects Kubernetes with external cloud providers when needed.

On each Node, the **kubelet** makes sure assigned Pods are running and healthy, while the **container runtime** does the actual work of creating and running their containers.

Pods can communicate directly using their IP addresses, but because Pod IPs can change, a **Service** can provide a stable address. Finally, **kube-proxy** configures the Node's networking rules so traffic sent to a Service can reach the appropriate Pods.

The important distinction is:

> **The Control Plane manages the cluster. Worker Nodes run the workloads.**

![[kubernetes-cluster-architecture.svg]]
*Figure from: https://kubernetes.io/docs/concepts/architecture/*

From here, we can start looking more closely at the objects Kubernetes actually manages, beginning with the **Pod**.

---

## Where Does the Control Plane Actually Run?

**Ok, this one will be important later.. **
Earlier, we learned about the Kubernetes cluster using a simplified architecture:

```text
Kubernetes Cluster
	 Control Plane
	 Worker Nodes
```

That view is useful because it separates their **responsibilities**: the Control Plane manages the cluster, while Worker Nodes run our application workloads.

But there's something that diagram intentionally doesn't show:

> **The Control Plane components are programs, so they also need machines to run on.**

**Why does this matter now?**
Kubernetes doesn't require every cluster to place these components in exactly the same way.  
**Think about it... that gives Kubernetes flexibility, that is great!**

The underlying infrastructure and the way a cluster is structured can vary greatly.
That means the actual distribution can vary depending on how the cluster is designed!

Kubernetes doesn't require every cluster to place these components in exactly the same way.

For example, the Control Plane components might run together on a single machine, or they can be distributed across multiple machines in a larger setup.

We don't need to worry about these different configurations yet.

![[control-plane-components-quote.png]]
*Source: https://kubernetes.io/docs/concepts/architecture/#control-plane-components*

What's important is understanding that the **Control Plane describes a responsibility within the cluster, not a specific machine**. Its components still need somewhere to actually run, and **where they run depends on how the cluster is set up**."

> [!info] Why does this matter?
> Right now, we're keeping things simple.
> The important thing to understand is that the **Control Plane still has to run somewhere**. Its components don't exist separately from the computers that make up our infrastructure.
>
> In a simple cluster, those components might run together on **one machine**. In larger clusters, they can be spread across **multiple machines**.
>
> You don't need to understand how that works yet.
>
> We'll come back to this later when we explore larger Kubernetes clusters.

---

# What's Next?

We now understand the basic architecture of Kubernetes. But what are we actually asking it to manage? 

The answer: **Kubernetes Objects!**

Objects describe what we want to exist in our cluster. 
We've already met one: the **Pod**.

Next, we'll explore more objects and see how Kubernetes uses them to turn our **desired state** into reality.

Continue on: [[Kubernetes - Kubernetes Objects (theory)]]
