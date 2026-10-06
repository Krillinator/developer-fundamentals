---
icon: LiWorkflow
---
# Virtual Machines & Hypervisors
![[Pasted image 20260918132227.png|368]]

When developing software locally, everything normally runs on **your computer**. Your operating system, installed software, network configuration and hardware all become part of your development environment.

*That's convenient, but sometimes we need something different.*

Maybe we need Linux while developing on Windows. Maybe we want to test something without changing our own computer. Or perhaps we need several independent machines that can communicate with each other.

This is where **Virtual Machines** become useful.

---

# Why Would We Need Another Computer?

![[Pasted image 20260918132328.png|190]]

Imagine you're developing an application on a Windows computer, but the server that will eventually run it uses Linux.

You could install Linux on another physical computer.

But now imagine you also need:
- another Linux server for testing
- an isolated machine for security experiments
- three separate machines for testing a cluster
- different operating system versions for debugging

Buying another physical computer every time would quickly become ridiculous.

What if we could instead create those computers **virtually**?

```text
Physical Computer
 Windows
 Linux VM
 Linux VM
 Linux VM
```

Each virtual machine behaves like a separate computer while actually using resources from the same physical machine.

This is the problem **virtualization** helps us solve.

---

# What Is a Virtual Machine?

A **Virtual Machine (VM)** is a virtual computer running on a physical computer.
Just like a physical computer, a VM can have its own:
- Operating system
- CPU allocation
- Memory
- Storage
- Network configuration
- Applications and files

The important difference is that these resources are **virtualized**.

For example, a VM might be configured with:

```text
Operating System: Ubuntu Linux
CPU:              2 virtual CPUs
Memory:           4 GB
Storage:          30 GB
```

Those resources ultimately come from the physical computer running the VM.

> [!NOTE]
> A VM behaves like its own computer, but it still depends on the hardware of the physical computer underneath it.

---

# What Is a Hypervisor?

We need software that can actually **create and manage** these virtual computers.

That software is called a **hypervisor**.

![[hypervisor-explained-geeksforgeeks.webp]]

Desktop applications such as **VMware Fusion**, **VMware Workstation** and **VirtualBox** are examples of hypervisors.

They sit between our physical computer and the virtual machines we create.

The hypervisor handles things such as allocating CPU and memory, providing virtual disks and networking, and starting or stopping our virtual machines.

> [!SUMMARY]
> **VM** = the virtual computer
>
> **Hypervisor** = the software that creates and manages virtual computers

---

# Why Are VMs Useful?

Virtual machines give us environments that can be separated from our normal computer.

This is useful when we want to experiment without filling our main operating system with dependencies and configuration changes. If something goes wrong inside the VM, the problem can remain isolated from the environment we normally work in.

They are also extremely useful for **learning and debugging**. We can reproduce another operating system, experiment with networking, create servers, break configurations and rebuild environments without needing another physical computer.

For example, one developer could use a single Windows computer to create several Linux machines and experiment with communication between them.

Suddenly one physical computer can behave like a small network of computers.

---

# VMs in Professional Environments

The same fundamental idea extends beyond local development.

Virtualization has long been used in professional infrastructure to allow physical servers to host multiple independent virtual machines. Cloud platforms also make extensive use of virtualization technologies to provide virtual computing resources.

The scale and technology may be different, but understanding local VMs gives us experience with several concepts that appear again in professional environments:

- Virtual hardware and operating systems
- Resource allocation
- Networking between machines
- Isolation
- Remote servers
- Infrastructure management

This makes VMs useful for much more than simply running another operating system.


---

# What Did We Learn?

A **Virtual Machine** behaves like a separate computer while using resources from a physical computer.

A **hypervisor** is the software responsible for creating and managing those virtual machines. VMware and VirtualBox are examples of software that provide this functionality.

Together, they allow us to create isolated environments for **development, testing, debugging and learning**, and they give us a practical way to experiment with infrastructure involving multiple machines.

---

# What's Next?

Now that we understand **why virtual machines exist**, we can look at the software used to create them.

For our local environments, we'll primarily look at:

- Mac users: [[VM - VMware]]
- Windows & Linux: [[VM - Virtual Box]]