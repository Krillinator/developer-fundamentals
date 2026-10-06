---
icon: LiLineChart
---
# Overview

A virtual machine doesn't have its own physical CPU or memory.

Instead, VMware allows us to decide how much of the **host computer's resources** should be available to the VM.

This means we can improve the VM's performance, but we need to be careful not to give it more resources than our physical computer can comfortably spare.

---

# How Much Should We Give the VM?

Imagine our computer has:

```text
CPU:     8 cores
Memory:  16 GB RAM
```

We could give Kali:

```text
CPU:     2 cores
Memory:  4 GB RAM
```

Those resources are then available for the virtual machine to use while it's running.

Giving Kali more resources can make applications and multitasking inside the VM faster, but allocating too much can leave the **host operating system** without enough resources.

> [!IMPORTANT]
> More isn't always better.
>
> The VM and the host ultimately share the same physical computer.

---

# Configure Processors & Memory

In VMware, open the virtual machine's settings and navigate to:

**Processors & Memory**

![[vm-performance-navigate-processors-memory.png]]

Here we can configure the amount of CPU and memory available to the VM.

![[vm-performance.png]]

### Processors

This determines how many **virtual CPU cores** are available to the VM.

More cores can help with workloads that can perform several tasks simultaneously, but they still rely on the physical processor underneath.

### Memory

This determines how much **RAM** is available to the VM.

Too little memory can make Kali slow, particularly when running a desktop environment and several applications. Giving it too much, however, can make the host computer struggle instead.

---

# Finding the Right Balance

There isn't one perfect configuration for every computer.

A lightweight Kali VM used for terminal work needs considerably fewer resources than a VM running browsers, development tools and several applications simultaneously.

Start with reasonable resources and **increase them when you actually need them**.

> [!TIP]
> If Kali feels slow, check its CPU and memory usage before immediately assigning more resources.
>
> The problem may be resource allocation, but it could also be storage, networking or something running inside Kali.

---

# What Did We Learn?

VMware lets us control how much **CPU and memory** a virtual machine can use.

These resources ultimately come from the physical host, so VM performance is a balancing act:

> **Enough resources for the VM to work comfortably, while leaving enough resources for the host to do the same.**

For a local learning environment, we generally don't need to maximize performance. We need the VM to be **comfortable and stable**.

---

# Other VM Configuration Worth Exploring

Once performance is configured, there are several other useful parts of the virtual machine worth understanding:

1. [[VMware Configuration - Network Settings]]
2. [[VMware Configuration - Encryption]] 
3. [[VMware Configuration - Disk Size]]
4. [[VMware Configuration - Performance]] - CURRENT
5. [[KALI - Password Reset]]
6. [[VMware Configuration - Snapshot]] 