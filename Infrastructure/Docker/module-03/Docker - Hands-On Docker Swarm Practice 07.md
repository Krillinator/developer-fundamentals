---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

In this module, we learned how to prepare an Ubuntu Server VM that can act as a Docker host for our Docker Swarm. Now you'll prepare another VM yourself.

This isn't a Docker challenge. The goal is simply to make sure the new machine has its **own identity and network address** before we eventually connect our machines together.

> **The goal is simple: create an Ubuntu Server VM, give it a unique hostname and identify its IP address.**

---

# 1. Create the Virtual Machine

Create a new **Ubuntu Server** virtual machine using the same configuration we've used previously.

If you're using the VM template we created earlier, create a **full clone** from that template.

A full clone gives the new VM its own independent virtual disk, allowing us to use it as a separate machine.

Once the VM has started, log in normally.

---

# 2. Give the Machine an Identity

Every machine in our upcoming cluster needs a name so that we can easily tell them apart.

For this machine, use:

```text
node-four
```

You can change the hostname with:

```bash
sudo hostnamectl set-hostname node-four
```

The hostname is the machine's name from the operating system's perspective.

You can verify it with:

```bash
hostname
```

> [!NOTE]
> Your terminal prompt may not immediately display the new hostname. Logging out and back in will update it.

---

# 3. Find the Machine's IP Address

A hostname tells **us** which machine we're working with, but our machines will also need a way to reach each other over the network.

Find the VM's IP address:

```bash
hostname -I
```

You may see something similar to:

```text
172.16.108.130
```

Your address will likely be different.

Write down the IP address of `node-one`. We'll need it when we start connecting our machines together.

| Machine     | IP Address         |
| ----------- | ------------------ |
| `node-four` | `________________` |

---

# 4. Verify the Machine

Before moving on, make sure you can answer both of these questions about your new VM:

| Question                             | Answer             |
| ------------------------------------ | ------------------ |
| What is the hostname of the machine? | `node-four`        |
| What is its IP address?              | `________________` |

You can always check these again with:

```bash
hostname
hostname -I
```

At this point, we have something important that we didn't have when working with Docker on a single computer:

**another independent machine on our network.**

---

# What Did We Practice?

In this lab, we prepared a machine that we'll later use as part of our Docker Swarm environment.

We created an independent Ubuntu Server VM, gave the machine a unique hostname and identified the IP address it uses on our network.

The important distinction is:

> **The hostname tells us which machine it is. The IP address gives other machines a way to reach it over the network.**

We haven't created a Docker Swarm yet. For now, we're simply preparing the infrastructure that we'll need when we do.

---

# What's Next?

Once each machine has its own hostname and IP address, we'll verify that they can communicate with each other. [[Docker - Hands-On Docker Swarm Practice 08]]