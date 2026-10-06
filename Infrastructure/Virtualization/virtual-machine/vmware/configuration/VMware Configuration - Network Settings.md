---
icon: LiNetwork
---
# Overview

A virtual machine needs a **virtual network adapter** if we want it to communicate with the internet, the host computer or other machines.

VMware gives us several ways to connect that adapter.

For our local environments, the two most important options to understand are:
- **Internet Sharing / NAT**
- **Bridged Networking**

They can both give the VM network access, but they connect the VM in different ways.

---

# How Should Our VM Connect to the Network?

Imagine our laptop is connected to our home network.
We start Kali and want it to access the internet.
One option is to let Kali access the network **through our host computer**.

Another option is to make Kali appear more like **another independent computer on the same local network**.

This is the main difference we're interested in.

---

# Open the Network Settings

Open the virtual machine's settings and navigate to:

**Network Adapter**

![[vm-network-settings-navigate-network-adapter.png|441]]

VMware displays the available networking modes for the VM.

![[vm-network-settings.png|444]]

The exact options can differ slightly between VMware products and versions, but the underlying concepts remain similar.

---

# Internet Sharing / NAT

For a normal local VM, **Internet Sharing** is usually the easiest option.

The VM is placed behind VMware's virtual networking and shares the host's network connection.

Conceptually:

```text
KALI VM - VMware NAT - Host Computer - Router / Network - Internet
```

The VM gets its own address inside VMware's virtual network, while VMware translates its traffic when it needs to communicate outside that network.

This is **Network Address Translation (NAT)**.

![[vm-network-settings-internet-sharing-good-for.png]]

> [!TIP] NAT is good for
> NAT is a great default when the VM mainly needs to **access the internet** for things such as downloading packages, browsing the web or installing software.
>
> It also keeps the VM somewhat separated from the physical local network.

For a normal Kali learning environment, this is often all we need.

---

# Bridged Networking

Sometimes we want the VM to behave more like an **independent machine connected directly to our local network**.

This is where **Bridged Networking** becomes useful.

Instead of hiding the VM behind VMware's NAT network, VMware bridges the VM's virtual network adapter to one of the host's physical network interfaces.

Conceptually:

```text
             Local Network
                  |
          -----------------
          |               |
     Host Computer      Kali VM
     192.168.1.10       192.168.1.20
```

The host and VM can therefore appear as separate machines on the same network.

![[vm-network-settings-bridged-networking-good-for.png]]

> [!TIP] Bridged Networking is good for
> Bridged networking is useful when the VM should behave like another computer on the network, particularly when testing **servers, network services, communication between machines or multi-machine environments**.

For example, another computer on the local network may be able to communicate directly with services exposed by the Kali VM, assuming the surrounding network and firewall configuration allow it.

---

# NAT vs Bridged

The easiest way to remember the difference is to think about **where the VM lives from the network's perspective**.

| | Internet Sharing / NAT | Bridged |
| --- | --- | --- |
| Internet access | Yes | Usually |
| VM uses VMware virtual network | Yes | No, it joins the physical network |
| Appears as separate machine on LAN | Usually no | Yes |
| Good default for ordinary VM use | **Yes** | Sometimes |
| Useful for network/server labs | Yes | **Especially** |

> [!SUMMARY]
> **NAT** → the VM accesses external networks through VMware and the host's connection.
>
> **Bridged** → the VM behaves more like another machine directly connected to the local network.

---

# Why Does This Matter for Our Labs?

For one Kali VM that simply needs internet access, **NAT is usually enough**.

Networking becomes more interesting when we start creating several virtual machines.

For example:

```text
VM 1
Docker Manager

VM 2
Docker Worker

VM 3
Docker Worker
```

Those machines need to communicate with each other.

At that point, understanding whether our VMs are connected through a **virtual NAT network**, a **bridged physical network**, or another VMware network becomes important.

> [!NOTE]
> Bridged networking isn't automatically "better" than NAT.
>
> Choose the network mode based on **who the VM needs to communicate with**.

---

# A Small Security Consideration

Bridged networking also changes the VM's exposure.

With NAT, the VM sits behind VMware's virtual networking.

With bridged networking, the VM behaves more like another device connected to the physical network.

That can be exactly what we want for networking experiments, but it also means we should think about what services the VM is running and which network we're connecting it to.

> [!WARNING]
> Be especially careful with bridged networking on **public or untrusted networks**.
>
> A security-focused VM may contain services and tools that you don't necessarily want exposed to other devices on that network.

---

# What Did We Learn?

VMware provides different ways for virtual machines to communicate with networks.

**Internet Sharing / NAT** is a good default when the VM mainly needs internet access. VMware places the VM on a virtual network and handles communication with external networks through NAT.

**Bridged Networking** connects the VM more directly to the physical network, allowing it to behave more like an independent computer alongside the host.

The important distinction is:

> **NAT → VM lives behind VMware's virtual networking.**
>
> **Bridged → VM joins the local network more like another physical machine.**

Neither is universally better. The correct choice depends on what we're trying to build.

---

# Other VM Configuration Worth Exploring

Once network settings are understood, there are several other useful parts of the virtual machine worth understanding:

1. [[VMware Configuration - Network Settings]] - CURRENT
2. [[VMware Configuration - Encryption]] 
3. [[VMware Configuration - Disk Size]]  
4. [[VMware Configuration - Performance]]
5. [[KALI - Password Reset]]
6. [[VMware Configuration - Snapshot]]