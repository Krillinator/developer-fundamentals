---
icon: LiGoal
---
# Introduction to Kubernetes & OpenShift

![[kubernetes-banner.jpg]]

> [!NOTE]- kubectl Version
> This course uses **kubectl v1.36.1**.
>
> Your version may differ, so some commands, fields or help text may change over time.
>
> When working with Kubernetes, use the built-in documentation such as `kubectl explain` and `--help`, and refer to the official Kubernetes documentation for the latest changes and deprecations:
>
> - **Kubernetes Documentation:** https://kubernetes.io/docs/
> - **Deprecation Guide:** https://kubernetes.io/docs/reference/using-api/deprecation-guide/

We've already seen how **Docker** can package an application and its dependencies into **containers**, making it easier to run the same application across different environments. 

We then took those containers beyond a single Docker host and introduced **container orchestration** with **Docker Swarm**, allowing multiple machines to work together as a cluster.

![[docker-swarm.png|348]]

But **Docker Swarm** was only our first introduction to orchestration. 
We're now going to explore **Kubernetes**, an open-source container orchestration platform designed to automate the deployment, scaling and management of containerized applications.

Many of the problems will already feel familiar: applications need to be distributed across machines, scaled when necessary, updated without unnecessary downtime and recovered when something fails. Kubernetes gives us another way of solving these problems and introduces a much larger ecosystem around them.

---

# Why Kubernetes?

Containerization isn't a new idea, but the release of **Docker in 2013** played a major role in making containers much more accessible to developers.

As containers became increasingly common, another problem became more important:

> **How do we manage all of these containers when applications grow beyond a single machine?**

Running a few containers manually is manageable. Running many containers across multiple machines while dealing with failures, updates, networking and scaling is a very different problem.

This is where **container orchestration** becomes important.

Kubernetes was originally developed at Google and later released as an open-source project. Today it sits at the center of a much larger **cloud-native ecosystem**, with technologies for networking, security, monitoring, service meshes and application platforms built around it.

In this course, we're going to explore both Kubernetes itself and some of that surrounding ecosystem.

---

# What Does Kubernetes Actually Do?

**We've already established the problem:** as the number of containers and machines grows, managing everything manually becomes increasingly difficult. **Kubernetes acts as the orchestrator that manages this for us.**

![[kubernetes-from-containers-to-kubernetes-diagram 1.png|394]]
*Architecture is simplified for clarity*

In the diagram above, we start with several **containerized workloads** that need somewhere to run. Kubernetes sits between those workloads and the machines that provide the resources needed to run them.

Those machines are called **nodes**.

![[Infrastructure/Kubernetes/res/svg/infrastructure_components/labeled/node.svg|150]]

A node is simply a **computer that Kubernetes can use to run our applications**. A node could be a physical computer, but it is commonly a virtual machine running locally or in the cloud.

Just like the Ubuntu Server VMs in our Docker Swarm setup became nodes, Kubernetes also uses nodes as the machines that make up its cluster and provide resources for running applications.

![[node-summary-diagram-overview.png]]

So the idea of a node isn't new to us. 
We've already used multiple machines as nodes in Docker Swarm. Kubernetes follows the same basic principle, but organizes and manages those nodes using its own architecture. 

**And for its roles and responsibilities?**
We've already seen this separation of responsibilities with **Docker Swarm**. 
The **manager** coordinated the cluster, decided where tasks should run and maintained the desired state, while the **worker nodes** provided the machines where those tasks and their containers actually ran. 

![[docker-swarm-explained-diagram.png]]

Kubernetes follows a similar overall idea, but uses different terminology and architecture. Instead of a Swarm manager coordinating worker nodes, Kubernetes has a **Control Plane** that coordinates the cluster, while **worker nodes** provide the resources where our application workloads run.

When multiple nodes are managed together by Kubernetes, they form a **Kubernetes cluster**.

**Many unanswered questions..**
We'll explore **Kubernetes architecture**, the resources Kubernetes uses to represent applications, how those resources can be described using **YAML**, and how we interact with a cluster using **kubectl**.

What happens if we want several copies running? What happens when demand increases? How can we update an application without stopping everything at once? And how should applications receive configuration or sensitive information?

Those questions will introduce concepts such as **ReplicaSets, autoscaling, rolling updates, ConfigMaps, Secrets and Service Bindings**. Some of which already sound familiar coming from the previous module. We'll dive deeper into their meaning later.

---

# Beyond Kubernetes

Kubernetes itself is only part of the picture.

A large ecosystem of tools and platforms has developed around it. Later in the course, we'll explore **Red Hat OpenShift**,

![[openshift-logo.png|235]]

an application platform built around Kubernetes, and **Istio**, 

![[istio-logo.jpeg|238]]

which introduces us to the idea of a service mesh.

We'll also encounter **Operators**, which provide a way of automating the management of applications running on Kubernetes.

The goal isn't to master every tool in the Kubernetes ecosystem immediately. It's to understand **where these technologies fit and what problems they're trying to solve**.

---

# Run Kubernetes Anywhere

Kubernetes is **open source** and isn't tied to one particular cloud provider.

It can be used with:
- Local development environments
- Private infrastructure
- Public cloud platforms
- Hybrid cloud environments

This portability is an important part of the cloud-native approach. Applications can be described and managed using Kubernetes concepts without designing everything around one particular infrastructure provider.

---

# Hands-On Learning

![[undraw_fill-the-blank_n29z.svg|312]]

A large part of this course will be practical.

We'll use hands-on labs to apply the concepts as they're introduced rather than only reading about them. The original course also provides a **Kubernetes lab environment free of charge**, so having your own Kubernetes cluster isn't required to complete its exercises.

Our previous Docker work will be useful here. We're not starting again from zero: concepts such as containers, images, registries, clusters, scaling, desired state and orchestration already give us a foundation for understanding what Kubernetes is doing.

---

# Prerequisites

The course assumes basic **computer and cloud literacy** and familiarity with core cloud concepts.

Being comfortable with the **command line and basic shell commands** will also be useful because we'll interact with Kubernetes from the terminal.

Our previous Docker modules already give us much of the container knowledge we'll need.

---

# Learning Objectives

After completing this course, you should be able to:
- Understand the benefits of containers.
- Build and run a container image.
- Understand Kubernetes architecture.
- Write a YAML deployment file.
- Expose a deployment as a Service.
- Manage applications with Kubernetes.
- Use ReplicaSets, auto-scaling, rolling updates and service bindings.
- Understand the benefits of OpenShift, Istio and other important cloud-native tools.

---

# Course Structure

The course is divided into four modules. We'll begin with container fundamentals before moving into Kubernetes architecture, application management and finally the wider Kubernetes ecosystem.

- **Module 1: Understanding the Benefits of Containers**
  - Introduction to Containers
  - Introduction to Docker
  - Building Container Images
  - Using Container Registries
  - Running Containers

- **Module 2: Understanding Kubernetes Architecture**
  - Understanding Container Orchestration
  - Understanding Kubernetes Architecture
  - Introduction to Kubernetes Objects & Components
  - Using Basic Kubernetes Objects
  - Using the `kubectl` command
  - Leveraging Kubernetes
  - Using ReplicaSets

- **Module 3: Managing Applications with Kubernetes**
  - Using Autoscaling
  - Understanding Rolling Updates
  - Understanding ConfigMaps and Secrets
  - Using Service Bindings

- **Module 4: The Kubernetes Ecosystem**
  - The Kubernetes Ecosystem
  - Introduction to Red Hat OpenShift
  - Red Hat OpenShift and Kubernetes
  - Builds
  - Operators
  - Istio

---

# What's Next?

We know what direction the course is taking, but before we start writing YAML or deploying applications, we need to understand the system we're about to use.

So we'll start with the fundamental question:

> **What exactly is Kubernetes, and how does it manage containerized applications?**

**Module 01 - Docker Recap**
1. [[Kubernetes - Understanding the Benefits of Containers (theory)]]
2. [[Kubernetes - Hands-On Container Practice 01]]
3. [[Kubernetes - Knowledge Check 01]]

**Module 02 - The components of Kubernetes**
1. [[Kubernetes - Module 02]]
2. [[Kubernetes - Kubernetes Components (theory)]]
3. [[Kubernetes - Kubernetes Objects (theory)]]
4. [[Kubernetes - Kubernetes Workloads (theory)]]
5. [[Kubernetes - Installation]]
6. [[Kubernetes - Hands-On kubectl Practice 02]]
7. [[Kubernetes - Exploring Our Kubernetes Cluster]]
8. [[Kubernetes - Hands-On Cluster Exploration Practice 03]]
9. [[Kubernetes - Deploying Our Applications]]
10. [[Kubernetes - Hands-On Deployment & Service Practice 04]]
11. [[Kubernetes - Knowledge Check 02]]
12. [[Kubernetes - Imperative vs Declarative (theory)]]

---

### Credits 
Icons from: https://github.com/kubernetes/community/tree/main/icons