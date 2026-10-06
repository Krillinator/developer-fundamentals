---
icon: LiContainer
---
# Containers Are Processes
![[red-container.png|305]]
At their core, containers are **isolated processes** running on a computer.

When we run:

```bash
docker run nginx
```

Docker doesn't create an entirely new computer here.
It starts Nginx as a **process** while using features of the operating system to **isolate** it.

The containers are isolated from each other, but their processes ultimately use the **same host kernel**.

---

# What Is a Kernel?

The **kernel is the core of an operating system**.
It sits between our applications and the computer's hardware:

![[kernel-explained.png|446]]

Applications don't normally control hardware and system resources directly.
Instead, the kernel manages things such as:

- **CPU**
- **Memory**
- **Processes**
- **Devices**
- **Filesystems**
- **Networking**

For example, when Nginx needs memory or wants to send network traffic, the **kernel manages those resources for it**.

This becomes important when understanding containers.
Containers don't each have their own kernel.

Instead, their processes share the **kernel of the host operating system**:

![[containers-and-kernel.png|395]]

> **Containers are not separate operating systems. They are isolated processes sharing the host's kernel.**

This is one of the reasons containers can be so lightweight.
But that creates another question:

> **If containers share the same kernel, how are they isolated from each other?**

That's where **Linux namespaces** come in.

---

# Linux Namespaces

> **If containers are just processes running on the same Linux system, how are they actually isolated from each other?**

Imagine we have two containers running:

```text
Container A
Nginx

Container B
Python
```

Both ultimately rely on the **same Linux kernel**.
So imagine if there was no isolation.
Nginx could potentially see and interract directly with the Python processes.

Both containers could see the same network.
They could see the same hostname.
They could see the same filesystem.

![[containers-no-isolation-example.png]]

That wouldn't feel very isolated.

Instead, we want something more like:

```text
Container A                  Container B

Nginx                        Python
Processes A                  Processes B
Network A                    Network B
Filesystem A                 Filesystem B
Hostname A                   Hostname B
```

Each container should feel like it has its **own environment**.

> **So how does Linux do this?**

Through something called **namespaces**.

---

# What Is a Namespace?

A **namespace gives a process an isolated view of part of the system**.

For example, Linux can give Container A its own view of running processes:

```text
Container A sees:

PID 1   nginx
PID 2   nginx-worker
```

While Container B might see:

```text
Container B sees:

PID 1   python
```

Both containers are still using the **same Linux kernel**, but they don't necessarily see the same processes.

And Linux can do this for more than just processes.

It provides different namespaces for different parts of the system:

| Namespace | Gives an isolated view of |
|---|---|
| `PID` | Processes and process IDs |
| `USER` | Users and group IDs |
| `UTS` | Hostname and domain name |
| `MNT` | Filesystem mount points |
| `NET` | Network interfaces, network stack and ports |
| `IPC` | Communication between processes |

So when we say a container is **isolated**, namespaces are a major part of what makes that possible.

> **Namespaces control what the processes inside a container can see.**

**But there's still another problem**
Imagine **Container A** starts consuming **all of the computer's RAM and CPU**.

Namespaces don't solve that.
For that, Linux has another feature: **cgroups**.

---

# cgroups

> **What happens if one container starts using all of our computer's resources?**

Imagine we have two containers: A & B
Namespaces keep their environments isolated.
But both containers still rely on the **same computer's resources**.

Now imagine Container A has a problem and starts consuming more and more memory.

![[containers-and-kernel-overclocking-overloaded.png|389]]

Eventually, it could leave very little memory available for Container B or other processes running on the computer.

> **Namespaces don't prevent this.**

Namespaces control what processes can **see**, not how many resources they can **use**.
This is where **cgroups** come in.

---

# What Are cgroups?

**cgroups**, short for **control groups**, are a Linux feature used to control and monitor the resources used by processes.

They can be used to control resources such as:

- CPU
- Memory
- Number of processes

For example, we could have:

```text
Container A
Memory limit: 512 MB

Container B
Memory limit: 1 GB
```

Both containers still use the same underlying computer.
But now one container can be prevented from consuming more resources than it's allowed.
So we now have two important Linux technologies working together:

| | Purpose |
|---|---|
| **Namespaces** | Control what processes can **see** |
| **cgroups** | Control how many resources processes can **use** |

Together, these help create the isolated environments we call **containers**.

These technologies aren't something Docker invented.
They are features provided by **Linux itself**.
Docker builds on technologies like these and gives us a much easier way to create and manage containers.

But before we get back to Docker, there's another important question:

> **If containers provide isolated environments, how are they different from Virtual Machines?**


---

# Containers vs Virtual Machines

![[virtual-machine.png|193]]

We now know that containers can give applications their own **isolated environments**.
But containers aren't the only technology that can do this.

You may have heard of **Virtual Machines**, or **VMs**.

> **If both containers and Virtual Machines provide isolated environments, what's actually the difference?**

Imagine we want to run two isolated applications on the same computer.
With **Virtual Machines**, we could create two completely separate virtual computers.
This means that each Virtual Machine gets its own:
- Operating System
- Kernel
- Applications
- Allocated resources

This provides strong isolation, but it also means we're running an entire operating system for every VM. 

---

# Containers Work Differently

Containers don't need a separate operating system and kernel for every isolated environment.
Instead, multiple containers can share the **same host kernel**.

So instead of:

![[virtual-machine-kernel-example.png]]

we have:

![[container-kernel-example.png]]

That's the fundamental difference.

| Virtual Machines           | Containers               |
| -------------------------- | ------------------------ |
| Virtualize entire machines | Isolate processes        |
| Each VM has its own OS     | Share the host OS kernel |
| Each VM has its own kernel | Share the same kernel    |
| More overhead              | Less overhead            |
| Slower to start            | Faster to start          |

---

# Why Are Containers So Lightweight?

Imagine starting three Virtual Machines.
Each one needs to start its own operating system:

```text
VM A
Application
Linux OS
Linux Kernel

VM B
Application
Linux OS
Linux Kernel

VM C
Application
Linux OS
Linux Kernel
```

That's a lot of duplicated infrastructure.

Containers don't need to do that:

![[container-kernel-example.png]]

They're essentially isolated groups of processes rather than entire virtual computers.

This means containers can generally:

- Start much faster
- Use fewer resources
- Take up less space
- Allow more isolated applications to run on the same machine

> **Virtual Machines isolate entire machines. Containers isolate processes.**

---

# Do Containers Replace Virtual Machines?

No.
In fact, they are commonly used **together**.

The Virtual Machine provides the virtual computer.
Containers then provide lightweight isolated environments for applications running inside that machine.

> [!note]
> We'll come back to why we might run containers inside Virtual Machines later.

For now, the important difference is:

> **VMs create virtual computers with their own operating systems and kernels.**

**Containers isolate processes while sharing a kernel.**


---

# So Why Does Docker Use Containers?

![[docker-banner.avif]]

> **If containers already exist as part of Linux technology, why do we need Docker?**

Imagine you've built an application and want to run it inside a container.

Without tools like Docker, you'd have to work much more directly with Linux features such as **namespaces, cgroups, filesystems and networking** yourself.

Most developers don't want to manually configure all of that every time they want to run an application. They want a simple way to package an application, create an isolated environment and run it.

Instead of manually configuring the underlying Linux technologies, we can simply run:

```bash
docker run nginx
```

Docker takes care of creating and configuring the container for us.

This is also where **Docker images** become important. 
An image packages the application together with the files and dependencies it needs. 
Docker can then use that image to create the container where the application runs.

This gives developers a consistent way to package and run applications across different environments and helps avoid the classic:

> *"But it works on my machine..."*

problem.

So when we talk about a **Docker container**, Docker hasn't invented a completely different kind of container.

---

# What's Next?

Now that we understand what containers actually are and what's happening underneath Docker, it's time to see some of those concepts **in practice**.

So far, we've learned that containers are made up of isolated processes and that Linux namespaces control what those processes can see. Instead of leaving those ideas as theory, we're going to start a container and inspect what actually happens inside it.

Along the way, we'll learn how to:

- Inspect a running container
- Start another process inside an existing container
- Interact with a container using Bash
- View the processes running inside a container
- See **PID namespace isolation** in practice
- Understand how processes inside a container relate to each other

Let's get back to the terminal and see what a container actually looks like while it'srunning.

Continue on: [[Infrastructure/Docker/module-01/containers/Docker - Hands-On Container Practice 01]]
