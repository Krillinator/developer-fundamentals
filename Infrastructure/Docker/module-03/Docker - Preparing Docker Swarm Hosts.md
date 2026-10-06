---
icon: LiCog
---
# Overview

![[undraw_local-server_9izb.svg]]

We're almost ready to create our first **Docker Swarm**.

By the end of this guide, we'll have:

```text
Node 1
 Ubuntu Server + Docker

Node 2
 Ubuntu Server + Docker

Node 3
 Ubuntu Server + Docker
```

All three machines will be running and able to communicate with each other.
Then we'll be ready to turn them into a Docker Swarm.

---

# Prerequisites

Before continuing, make sure you have done the following:
- Completed **Docker Module 02** [[Docker - Module 02]]
- Installed a **hypervisor** [[VM - Start Here]]
- Downloaded the correct **Ubuntu Server ISO** [[VM - Ubuntu Server for Docker Swarm]]
- Configured your Ubuntu Server virtual machine [[VM - Ubuntu Server for Docker Swarm]]
- Internet access from the virtual machines [[VM - Ubuntu Server for Docker Swarm]]

---

# Create Three Docker Hosts

Our `docker-template` machine now contains everything our Docker Swarm nodes need in common:
- Ubuntu Server
- Docker Engine
- OpenSSH Server
- The `dockeradmin` user
- Working network configuration

We *could* create three new virtual machines from the Ubuntu ISO and repeat the entire installation process for each one.

But we've deliberately prepared `docker-template` so we don't have to.
Instead, we'll use our prepared VM as the starting point for all three machines.

Each copy starts with the same operating system and software, but we'll give each machine its own identity before creating the Docker Swarm.

---

## Shut Down the Template

Before copying the virtual machine, make sure it is completely shut down.

From Ubuntu:

```bash
sudo shutdown -h now
```

Wait until your hypervisor shows that the virtual machine is powered off.

> [!IMPORTANT]
> Don't create the copies while the template VM is running. We want to copy the virtual machine from a clean, powered-off state.

---

## Create the Three Copies

In your hypervisor, locate the Ubuntu Server VM we've prepared.
If you haven't already, rename the VM in VMware Fusion to something that makes its purpose clear, such as:

```text
docker-template-ubuntu-server
```

![[hypervisor-full-clone-dropdown-selected.png|354]]

Make sure the template VM is **completely shut down** before continuing.
Right-click the VM and select:

```text
Create Full Clone...
```

![[hypervisor-full-clone-dropdown-selected.png|274]]

### Why a Full Clone?

A **full clone** creates an independent copy of the virtual machine, including its own virtual disk.
This is useful for our Docker Swarm because each node should behave as its own machine.

> [!INFO]- What About a Linked Clone?
> A **linked clone** shares parts of the original VM's virtual disk and only stores its own changes.
>
> This uses less storage, but the clone remains dependent on the original VM.
>
> A **full clone** uses more storage but gives us an independent VM, which is what we'll use for our Docker Swarm nodes.

Create three full clones and name them:

```text
node-one
node-two
node-three
```

Once finished, VMware Fusion should contain something similar to:

![[hypervisor-full-clone-x3-result .png|347]]

> [!TIP]
> Keep `docker-template-ubuntu-server` unchanged.
>
> If we break one of our nodes later, we still have a clean Ubuntu Server + Docker environment that we can use to create another one.


---

# Configure the Cloned Machines

We'll configure our machines one at a time, starting with:

```text
node-one
```

Start `node-one` in your hypervisor and log in using the account inherited from our template.
Once logged in, check the current hostname:

```bash
hostname
```

Even though VMware calls this virtual machine:

```text
node-one
```

Ubuntu may still report:

![[hostname-node-1-still-shows-docker-template.png|299]]

Why?

Because these are two separate things:
* Hypervisor VM name - node-one
* Ubuntu hostname - docker-template

Renaming or cloning a virtual machine in VMware doesn't automatically change the operating system's hostname.

The hostname was stored **inside Ubuntu**, so it was copied along with everything else.

---

## Change the Hostname

For `node-one`, change the Ubuntu hostname:

```bash
sudo hostnamectl set-hostname node-one
```

Verify it:

```bash
hostname
```

It should now return:

```text
node-one
```

We'll repeat this for each machine:

| VMware VM | Ubuntu Hostname |
| --- | --- |
| `node-one` | `node-one` |
| `node-two` | `node-two` |
| `node-three` | `node-three` |

Start all three VMs.
On each machine, check its hostname and IP address:

```bash
hostname
hostname -I
```

We should have three different hosts with three different IP addresses, for example:

```text
node-one     172.16.108.130
node-two     172.16.108.131
node-three   172.16.108.132
```

Your IP addresses will likely be different.


---

## Test the Network

From `node-one`, verify that the other nodes are reachable:

```bash
ping -c 3 <node-two-IP>
ping -c 3 <node-three-IP>
```

Finally, verify Docker:

```bash
docker --version
```

![[hostname-ping-verification-network-reachable.png|422]]

If each machine has its own hostname and IP address, Docker is running, and the machines can reach each other, our three Docker hosts are ready.

> [!SUCCESS]
> We now have **three independent Docker hosts** ready to form our Docker Swarm.


---

## Verify Docker Access

Finally, make sure our `dockeradmin` user can use Docker on **each node** without `sudo`.

Run:

```bash
groups
```

This lists the groups the currently logged-in user belongs to.

Look for:

```text
docker
```

For example:

![[verify-groups-docker.png|454]]

The important part is that **`docker` appears somewhere in the list**.


---

# Docker Swarm Networking

Docker Swarm requires several types of communication between its nodes.
	Docker uses the following ports by default:

| Port | Protocol | Purpose |
| --- | --- | --- |
| **2377** | TCP | Swarm management |
| **7946** | TCP/UDP | Communication between nodes |
| **4789** | UDP | Overlay network traffic |

These ports need to be reachable **between the nodes participating in the Swarm**.

For our local VM environment, the important thing is that the three machines can communicate with each other over their virtual network.

> [!WARNING]
> Swarm networking ports should be available between trusted Swarm nodes, not unnecessarily exposed to untrusted networks.

Docker's Swarm networking requirements:
https://docs.docker.com/engine/swarm/swarm-tutorial/

---

# Our Environment

We started with three empty virtual machines.

We now have:

![[node-summary-diagram-overview.png]]

Each machine can:
- Run Docker containers
- Access the network
- Communicate with the other nodes

At the moment, Docker is running independently on each machine.

```text
node1        node2        node3
Docker       Docker       Docker

Independent   Independent   Independent
```

Nothing is coordinating them yet. 
This is where **Docker Swarm** comes in.

---

# What Did We Learn?

We prepared the infrastructure that Docker Swarm will use.

We:

- Created three Ubuntu Server virtual machines
- Gave each machine its own hostname
- Updated Ubuntu
- Installed Docker Engine on every node
- Verified that Docker works
- Identified each node's IP address
- Verified that the machines can communicate
- Identified the network ports Docker Swarm uses

We now have **three Docker hosts**.

The next step is to make them work together.

---

# What's Next?

Our three Docker hosts are ready.

Now we'll take:

```text
node1
node2
node3
```

and turn them into:

```text
Docker Swarm
 node1    Manager
 node2    Worker
 node3    Worker
```

To understand fault tolerance, single points of failure and Docker's recommendations for a reliable Swarm, we first need to understand how Docker Swarm works.

Continue with: [[Docker - Understanding Docker Swarm (theory)]]
