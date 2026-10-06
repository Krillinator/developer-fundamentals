---
icon: LiBox
---
# Table of Contents

- [[#Why Ubuntu Server?|Why Ubuntu Server?]]
- [[#Visit: Download Ubuntu Server|Download Ubuntu Server]]
- [[#Which Architecture?|Which Architecture?]]
	  - [[#Windows|Windows]]
	  - [[#Linux|Linux]]
	  - [[#Apple Silicon|Apple Silicon]]
- [[#Architecture Summary|Architecture Summary]]
- [[#Which ARM Image?|Which ARM Image?]]
- [[#What Are We Downloading?|What Are We Downloading?]]
- [[#Open the ISO in Your Hypervisor|Open the ISO in Your Hypervisor]]
- [[#Configure the Virtual Machines|Configure the Virtual Machines]]
- [[#Run Ubuntu Server Installation|Install Ubuntu Server]]
- [[#Install Docker Engine|Install Docker Engine]]
	  - [[#Update Ubuntu|Update Ubuntu]]
	  - [[#Add Docker's Repository|Add Docker's Repository]]
	  - [[#Give APT a Way to Verify Docker|Verify Docker]]
	  - [[#Add Docker's Signing Key|Add Docker's Signing Key]]
	  - [[#Add Docker's Repository to APT|Add Repository to APT]]
- [[#Test Docker|Test Docker]]
- [[#Run Docker Without sudo|Run Docker Without sudo]]
- [[#Verify Our Docker Host|Verify Our Docker Host]]
- [[#Save the Clean Docker Host|Save the Clean Docker Host]]
- [[#What Did We Learn?|What Did We Learn?]]
- [[#What's Next?|What's Next?]]

---
# Overview

![[servicos-ubuntu-server.jpg|351]]

In this module, we're going to create a **Docker Swarm** consisting of multiple machines.
Each machine participating in our Docker Swarm will eventually become a **node**.

> **A node is a machine participating in a cluster.**

For our nodes, we'll use **Ubuntu Server** and include a step for installing Docker inside of our node.

---

# Why Ubuntu Server?

Ubuntu comes in several editions, including **Ubuntu Desktop** and **Ubuntu Server**.
For our Docker Swarm nodes, we don't need a desktop environment or graphical applications as we'll primarily interact with our nodes through the terminal.

We mainly need:
- Linux
- Networking
- Docker
- A terminal

Ubuntu Server gives us exactly that.

---

# Visit: Download Ubuntu Server

![[ubuntu-download-server-wep-page.png]]

Visit the official Ubuntu Server download page:
https://ubuntu.com/download/server

Ubuntu provides the latest **LTS** version of Ubuntu Server.
**LTS** stands for: **Long-Term Support**

> [!IMPORTANT]
> Before downloading, we need to make sure we're choosing the correct **CPU architecture**.

---

# Which Architecture?

Ubuntu Server is available for different processor architectures.
The two we're interested in are:
* amd64
* arm64

The correct image depends primarily on the processor architecture of the computer running our virtual machines.

Let's explore.

---

## Windows

![[Terminal - Windows os png.png|274]]

Most Windows computers use an **Intel or AMD processor**.
For these computers, download:

```text
amd64
```

Despite the name `amd64`, this architecture is used by modern **Intel and AMD 64-bit processors**
You may also see it called:

```text
amd64
x86_64
x64
```

For our purposes, these refer to the same 64-bit x86 architecture.

> [!TIP] Typical Windows PC
> **Intel processor** - amd64
>
> **AMD processor** - amd64

---

## Linux

![[Terminal - Linux os png.png|229]]

The same rule applies to Linux.
If your Linux computer uses a normal Intel or AMD processor, download:

```text
amd64
```

If the computer itself uses a 64-bit Arm processor, download:

```text
arm64
```

If you're unsure, you can check from the terminal:

```bash
uname -m
```

You may see:

```text
x86_64
```

which means:

```text
amd64
```

Or:

```text
aarch64
```

which means:

```text
arm64
```

---

## Apple Silicon

![[mac-logo.jpg|293]]

Modern Macs using Apple Silicon use the **Arm architecture**.
This includes:

```text
M1
M2
M3
M4
M5
...
```

For an Apple Silicon Mac, download:

```text
arm64
```

The ARM version of Ubuntu Server is available from:

https://ubuntu.com/download/server/arm

---

# Architecture Summary

| Host computer | Ubuntu Server image |
| --- | --- |
| Windows + Intel | **AMD64** |
| Windows + AMD | **AMD64** |
| Linux + Intel | **AMD64** |
| Linux + AMD | **AMD64** |
| Linux + Arm | **ARM64** |
| Intel Mac | **AMD64** |
| Apple Silicon Mac | **ARM64** |

---

# Which ARM Image?

On the Ubuntu Server ARM download page, you may see more than one ARM image.
For example:

```text
Ubuntu Server
Ubuntu Server (64k page size)
```

For our virtual machines, use the normal: **Ubuntu Server**

The standard ARM64 image is the general-purpose option.
We don't need the specialized **64k page size** image for this lab.

---

# What Are We Downloading?

![[download-iso-example.png|353]]

Ubuntu Server is downloaded as an:

```text
.iso
```

For example:

```text
ubuntu-26.04.1-live-server-amd64.iso
```

or:

```text
ubuntu-26.04.1-live-server-arm64.iso
```

An **ISO** is essentially installation media.
Think of it as the virtual equivalent of inserting an operating system installation into a computer.

---

# Open the ISO in Your Hypervisor

Once the ISO has finished downloading, don't try to install Ubuntu directly on your physical computer.
	Instead, we'll give the ISO to our **hypervisor**.

If you don't have a **hypervisor** installed, check out [[VM - Start Here]]

---

# Configure the Virtual Machines

We'll eventually create multiple Ubuntu Server virtual machines for our Docker Swarm.
To keep the lab consistent, we'll give each machine the same basic configuration:

| Setting | Configuration |
| --- | --- |
| **CPU** | 2 cores |
| **Memory** | 2 GB |
| **Disk** | 20 GB |
| **Network** | NAT / Internet Sharing |
| **Operating System** | Ubuntu Server |
These settings give Ubuntu Server and Docker enough resources for our lab without allocating unnecessary resources from the host computer.

> [!NOTE]
> We're deliberately keeping the virtual machines relatively lightweight because we'll eventually run **multiple nodes at the same time**.
>
> Three VMs with 2 GB of memory each, for example, require around 6 GB of RAM for the virtual machines rather than 12 GB if we gave each one 4 GB.

--- 
## Network

For now, keep the network adapter configured using **NAT / Internet Sharing**.

This gives our Ubuntu Server VMs internet access while keeping them on VMware's virtual network.

Later, before creating the Docker Swarm, we'll verify that our nodes can communicate with each other.

> [!TIP]
> If your hypervisor uses slightly different terminology, that's okay.
>
> VMware Fusion may call this **Share with my Mac**, while other hypervisors commonly refer to it as **NAT**.

The goal is that we all begin the Docker Swarm lab with roughly the same virtual machine configuration.

--- 

# Run Ubuntu Server Installation

Our virtual machine is configured and the Ubuntu Server ISO is attached.
Now we're ready to actually install the operating system.
Start the virtual machine.

When the VM starts, it will boot from the Ubuntu Server ISO.

You'll first see the **GRUB boot menu**.

![[gnu-grub-install-ubuntu-server-terminal-window.png]]

Select:

```text
Try or Install Ubuntu Server
```

and press:

```text
Enter
```

> [!NOTE]
> If you don't select anything, the highlighted option may start automatically after a short countdown.

Ubuntu will now load the Server installer.

---

## Choose a Language

The first installer screen asks which language we want to use during the installation.

![[gnu-grub-install-ubuntu-server-language-select.png]]

For this guide, select:

```text
English
```

Then press:

```text
Enter
```

You're free to choose another language, but the screenshots and terminology throughout this guide will use **English**.

---

## Configure the Keyboard

Next, Ubuntu asks for our keyboard layout.

![[gnu-grub-install-ubuntu-server-keyboard-layout.png]]

Choose the layout that matches your physical keyboard.

For example:

```text
Layout:   English (US)
Variant:  English (US)
```

For example, if you're using a Swedish keyboard:

```text
Layout:   Swedish
Variant:  Swedish
```

The keyboard layout does not need to match the language used by Ubuntu.
For example, we can use the Ubuntu installer in **English** while using a **Swedish keyboard layout**.

---

## Choose the Installation Type

Next, Ubuntu asks which type of Server installation we want.

![[gnu-grub-install-ubuntu-server-type.png]]

Select:

```text
Ubuntu Server
```

Ubuntu also provides:

```text
Ubuntu Server (minimized)
```

The minimized version installs fewer packages and is intended for environments where a very small runtime footprint is preferred.

For our Docker nodes, we'll use the normal **Ubuntu Server** installation. It gives us a more convenient general-purpose server environment while we're learning and working directly with the machines.

You'll also see:

```text
[ ] Search for third-party drivers
```

Leave this **unchecked** for our virtual machines.

Our VM uses virtual hardware provided by the hypervisor, so we don't need Ubuntu to search for additional proprietary hardware drivers during this installation.

Then select **Done** and continue.

---

## Configure the Network

Next, Ubuntu asks us to configure the server's network connection.

![[gnu-grub-install-ubuntu-server-network.png]]

In our case, VMware has already provided the virtual machine with a network connection.
Ubuntu shows something similar to:

```text
enp2s0
DHCPv4   172.16.108.129/24
```

Let's break that down:

| Value | Meaning |
| --- | --- |
| `enp2s0` | The VM's network interface |
| `DHCPv4` | The IP address was assigned automatically |
| `172.16.108.129` | The VM's current IPv4 address |
| `/24` | The size of the network it belongs to |

Because we configured VMware to use **NAT / Internet Sharing**, VMware provides a virtual network that our Ubuntu Server can use.

For now, leave the network configuration as it is and select: DONE

> [!NOTE]
> Your IP address will probably be different from the one shown in this guide.
> That's completely normal.

---
## THEORY: Why Does the IP Address Matter?

Right now, the address isn't particularly exciting.
Later, however, we'll have three machines:

```text
node1    172.16.108.x
node2    172.16.108.x
node3    172.16.108.x
```

For Docker Swarm to work, these machines need to be able to **communicate with each other over the network**.

We'll return to their IP addresses when we're ready to connect the machines together.

---

## Proxy Configuration

Next, Ubuntu asks whether the server needs a **proxy** to access the internet.

![[gnu-grub-install-ubuntu-server-proxy.png]]

A proxy is an intermediary that network traffic can be sent through before reaching the internet.

```text
	Server - Proxy - Internet ->
```

Proxies are sometimes used in corporate, school or otherwise managed networks.
For a normal home network, we don't need one.

Leave:

```text
Proxy address:
```

blank and select:

```text
Done
```

> [!NOTE]
> If your organization requires a proxy to access the internet, you would enter its address here. Otherwise, leave this field empty.

---

## Ubuntu Archive Mirror

Next, Ubuntu asks which **archive mirror** it should use.

![[gnu-grub-install-ubuntu-server-mirror-address.png]]

A mirror is a server that provides copies of Ubuntu's software packages and updates.

Instead of every Ubuntu machine downloading packages from one central server, Ubuntu maintains mirrors in different locations.

The installer will normally select an appropriate mirror automatically.
The installer tests the selected mirror before continuing.

Look for:

```text
This mirror location passed tests.
```

If the test succeeds, we don't need to change anything.

> [!NOTE]
> We'll later use Ubuntu's package manager, `apt`, to install and update software.
> The archive mirror is one of the places `apt` can retrieve Ubuntu packages from.

---

## Configure Storage

Next, Ubuntu asks how we want to configure storage.

![[gnu-grub-install-ubuntu-server-guided-storage.png]]

Remember that our VM has its own **virtual disk**:

```text
Physical Computer
 VMware VM
    └ 20 GB Virtual Disk
```

Ubuntu sees this virtual disk as if it were a normal disk installed in a physical computer.
For our Docker lab, select:

```text
(X) Use an entire disk
[X] Set up this disk as an LVM group
[ ] Encrypt the LVM group with LUKS
```

Leave **Custom storage layout** unselected.

> [!IMPORTANT]
> **Use an entire disk** refers to the VM's **20 GB virtual disk**.
>
> It does **not** erase the disk or operating system on your physical computer.

### What Is LVM?

**LVM** stands for **Logical Volume Manager**.

Normally, storage is divided directly into fixed partitions.
LVM adds another layer between the physical disk and the filesystems:
	 Virtual Disk - LVM - Filesystem

This makes storage easier to organize and resize later.

Ubuntu Server enables LVM by default here, so we'll keep it enabled.

> [!NOTE]
> We don't need to understand LVM in depth for Docker Swarm. For this lab, the default Ubuntu storage configuration is perfectly fine.

### Disk Encryption

You'll also see:

```text
[ ] Encrypt the LVM group with LUKS
```

**LUKS** provides disk encryption.

We won't enable it for these temporary learning VMs because it would add another password and additional setup that isn't relevant to Docker Swarm.

Leave it unchecked and select:

```text
Done
```

> [!INFO]- When Is Disk Encryption Useful?  
> Disk encryption is useful when the data stored on a machine should remain protected even if someone gains physical access to its storage.  
>  
> Imagine a laptop containing sensitive files is stolen.  
>  
> Without disk encryption, someone could potentially remove the drive or boot another operating system and try to read the files directly.  
>  
> With disk encryption, the data stored on the disk is encrypted and can't normally be read without the encryption key or passphrase.  
>  
> Common examples include:  
>  
> - Laptops that could be lost or stolen  
> - Servers containing sensitive information  
> - Company devices  
> - Systems storing customer or personal data  
> - Virtual machines containing sensitive data  
>  
> **A login password protects access to your user account. Disk encryption protects the data stored on the disk.**  
>  
> For our disposable Docker Swarm VMs, we don't have sensitive data to protect, so we'll keep encryption disabled.

> [!INFO]- What Is a Custom Storage Layout?
> A **custom storage layout** lets us manually decide how Ubuntu should organize the virtual disk instead of letting the installer configure it automatically.
>
> For example, we could manually control:
>
> - Partitions and their sizes
> - Filesystems
> - Mount points
> - LVM configuration
>
> This can be useful when a server has specific storage requirements, such as keeping application data or logs on separate partitions.
>
> For our Docker Swarm nodes, we don't have any special storage requirements.
>
> **Guided storage gives us everything we need, so we'll leave Custom storage layout unselected.**

---

## Review the Storage Configuration

Ubuntu now shows the storage layout it plans to create.

![[gnu-grub-install-ubuntu-server-file-system-summary.png]]

This is our chance to review the configuration before Ubuntu actually writes the changes to the virtual disk.

At the top, we can see the main filesystems:

| Mount Point | Purpose |
| --- | --- |
| `/` | Main Linux filesystem |
| `/boot` | Files needed to start Ubuntu |
| `/boot/efi` | Files used by UEFI to boot the system |

Our main filesystem:

```text
/
```

has been created inside the **LVM volume group** we enabled in the previous step.

### Why Does It Say 7.316 GB Free?

Our virtual disk is:

```text
20 GB
```

but Ubuntu has initially allocated approximately:

```text
10 GB → /
1.75 GB → /boot
953 MB → /boot/efi
```

Some additional space remains available inside the LVM volume group.
That's okay.

One advantage of LVM is that storage can be kept available and assigned to logical volumes later if needed.

> [!NOTE]
> We don't need to modify the storage layout for this Docker Swarm lab.
> The automatically generated configuration is sufficient.

Review the configuration and select DONE.
Ubuntu will ask us to confirm before writing these changes to the virtual disk.

> [!INFO]- What If We Need More Storage Later?
> The storage size isn't permanently fixed.
>
> In our current configuration, the **20 GB virtual disk** contains an LVM volume group with some space that hasn't been allocated to `/` yet:
>
> ```text
> 20 GB Virtual Disk
> 	/boot/efi
> 	/boot
> 	LVM
> 	    /        10 GB
> 	    Free     ~7.3 GB
> ```
>
> If `/` needs more space later, we can extend it using the free space already available inside LVM.
>
> *But what if the entire **20 GB virtual disk** becomes too small?*
> We can increase the size of the virtual disk in our hypervisor and then make that additional space available to Ubuntu and LVM.
>
> Think of it as two layers:
> 	**Hypervisor** - controls how large the virtual disk is  
> 	**Ubuntu / LVM** - controls how the space inside that disk is used
>
> So we don't need to perfectly predict how much storage we'll need when creating the VM.

---

## Create the Template Profile

Next, Ubuntu asks us to create a user account and give the server a name.

![[gnu-grub-install-ubuntu-server-file-profile-creation.png]]

Normally, we'd configure these values for the individual server we're installing.

**However..**
This machine will contain the configuration that all of our Docker nodes have in common. We'll later use it to create three separate machines.

For our lab, use a generic administrative account:

```text
Your name:          Docker Administrator
Your server's name: docker-template
Pick a username:    dockeradmin
Choose a password:  docker123
```

The important part is that we're deliberately **not** calling this machine `node1`.

Our current machine represents:

```text
docker-template
	Ubuntu Server
	Common configuration
	Docker Engine (later)
```

After the template is ready, we'll use it to create:

```text
docker-template
	node1
    node2
	node3
```

Each node will receive its own machine identity before we create the Docker Swarm.

> [!INFO]- How Would This Work Professionally?
> Imagine a company needs to deploy 100 identical Linux servers.
>
> Installing Ubuntu and configuring every server manually would be slow and inconsistent.
>
> Instead, organizations commonly start from a **standardized machine image** containing the operating system and common software.
>
> When a new machine is provisioned, automation can provide the configuration that belongs specifically to that machine, such as:
> - Hostname
> - Network configuration
> - User access
> - SSH keys
> - Credentials or secrets
> - Environment-specific configuration
>
> Tools and platforms such as **cloud-init, Terraform, Ansible and cloud VM services** can participate in this process.
> [!INFO]- Going Further: Automating VM Provisioning
> For this lab, we're creating our Ubuntu Server interactively.
>
> This means the template will contain the `dockeradmin` account and password we create here. When we later copy the template, our new machines will initially inherit that configuration.
>
> That's convenient for a small learning environment, but there are more flexible ways to create and configure machines.
>
> In professional environments, this process is often **automated**.
>
> Instead of manually installing and configuring every machine, we can describe what should be created and provide machine-specific configuration when each machine is provisioned.
>
> Some technologies worth exploring are:
>
> - **cloud-init** - configures a machine when it first starts, such as its hostname, users, SSH keys and packages.
> - **Terraform** - defines and creates infrastructure such as virtual machines, networks and storage using code.
> - **Ansible** - configures software and settings across machines after they're available.
>
> A more automated workflow might therefore look like:
>
> ```text
> Reusable Image
>       Provision - node1 + unique configuration
>       Provision - node2 + unique configuration
>       Provision - node3 + unique configuration
> ```
>
> This lets us reuse the things our machines should have **in common** without manually giving every machine the same identity and credentials.
>
> We won't automate provisioning in this Docker module because we want to focus on **Docker Swarm**, but these technologies are a natural next step if you want to explore infrastructure automation.


---

## Ubuntu Pro

Next, Ubuntu offers **Ubuntu Pro**.

![[gnu-grub-install-ubuntu-server-ubuntu-pro.png]]

Ubuntu Pro is an optional Canonical service that provides additional security maintenance and features aimed particularly at organizations with extended support, compliance and security requirements.

For our Docker Swarm lab, we don't need it.

> [!NOTE]
> Ubuntu Pro can be enabled later if needed. Skipping it here doesn't prevent us from using Ubuntu Server normally or installing Docker.


---

## SSH Configuration

Next, Ubuntu asks whether we want to install **OpenSSH Server**.

![[gnu-grub-install-ubuntu-server-ssh.png]]

**SSH (Secure Shell)** allows us to securely open a terminal on another machine over the network.

Without SSH, we'd interact with each node through its virtual machine window.
With SSH, we can connect directly from our host computer.

This will be very useful once we're working with several Docker nodes.

Select:

```text
[X] Install OpenSSH server
```

For our local lab, we can also enable:

```text
[X] Allow password authentication over SSH
```

This means we'll be able to connect using the username and password we created earlier.

> [!INFO]- Passwords vs SSH Keys
> SSH doesn't require us to authenticate using a password.
>
> A more common approach for administering servers is **SSH key authentication**.
>
> ```text
> Your Computer
>  Private key - kept secret
>  Public key - added to the server
> ```
>
> When connecting, the server can verify that we possess the corresponding private key without sending or storing our login password as the SSH credential.
>
> Ubuntu even gives us an **Import SSH key** option during installation.
>
> We'll use password authentication for this isolated Docker lab to keep the setup simple, but SSH keys are worth exploring when moving toward more realistic server administration.

> [!INFO]- When Would You Import an SSH Key?
> Importing is useful when you **already have an SSH key** on another service, such as GitHub or Launchpad. Ubuntu can retrieve that existing public key and configure the server to accept it, saving you from manually copying the key onto the server. If you don't already have a suitable key, there's little reason to import one; you can generate a new key yourself instead.

> [!INFO]- What Are Authorized Keys?
> **Authorized keys** are the public SSH keys that are allowed to log in to this account. When you import an SSH key, Ubuntu adds the public key to this list, allowing the corresponding private key to authenticate without using the account password.

---

## Featured Server Snaps

Ubuntu gives us the option to install additional server software during installation.

![[gnu-grub-install-ubuntu-server-snaps.png]]

These are optional applications distributed as **Snap packages**, such as MicroK8s, Nextcloud, AWS CLI and PowerShell.

We don't need any of these for our Docker Swarm environment. Docker Engine will be installed separately using Docker's official repository.

Leave everything **unchecked** and select done.

> [!INFO]- What Is a Snap?
> A **Snap** is a software package format developed by Canonical. Snap packages bundle an application with many of its dependencies and can be installed and updated through Ubuntu's Snap system. They're simply another way of distributing software and aren't required for Docker.

> [!INFO]- What Are These Featured Server Snaps?
> These are optional applications Ubuntu can install for us during setup:
>
> - **MicroK8s** - A lightweight Kubernetes distribution from Canonical.
> - **Nextcloud** - A self-hosted platform for file storage, sharing, calendars and other cloud services.
> - **Wekan** - An open-source Kanban board for organizing tasks and projects.
> - **Canonical Livepatch** - Applies certain Linux kernel security updates without requiring a reboot.
> - **Mosquitto** - An MQTT message broker commonly used for communication between IoT devices.
> - **etcd** - A distributed key-value store used to store configuration and cluster state, notably by Kubernetes.
> - **PowerShell** - Microsoft's cross-platform command shell and scripting language.
> - **SABnzbd** - An automated Usenet download client.
> - **Wormhole** - A tool for securely transferring files or text between computers.
> - **AWS CLI** - A command-line interface for managing Amazon Web Services.
> - **SLCLI** - A command-line interface for managing IBM Cloud infrastructure originally based on SoftLayer.
> - **doctl** - DigitalOcean's command-line interface for managing its cloud resources.
> - **Keepalived** - Provides high-availability and load-balancing features for Linux systems.
> - **LXD** - A platform for managing Linux system containers and virtual machines.
>
> None of these are required for our Docker Swarm environment, so we'll leave them unchecked.

---

## Complete the Installation

Ubuntu Server has now been installed and the initial system configuration is complete.

![[gnu-grub-install-ubuntu-server-reboot-install-complete.png]]

The installer has also applied the configuration we selected during setup, including installing **OpenSSH Server** and downloading available security updates.

Time to reboot.
The virtual machine will restart and boot into our newly installed Ubuntu Server system.

After rebooting, we no longer need the Ubuntu ISO to install the operating system. Ubuntu is now installed on the VM's virtual disk.

![[gnu-grub-install-ubuntu-server-reboot-remove-install-medium.png|519]]

The **installation medium** is the Ubuntu ISO we used to install the operating system.  
  
If the ISO is still connected to the virtual CD/DVD drive, disconnect it from your hypervisor. Some hypervisors, including VMware, may already have disconnected it automatically.  
  
The virtual machine will now boot from its virtual disk, where Ubuntu Server is installed.  
  
> [!NOTE]  
> You don't need to delete the Ubuntu ISO from your computer. We're only making sure it is no longer mounted to the virtual machine.

> [!INFO]- What's Happening During the First Boot?
> During the first boot, you may see **cloud-init** performing some final configuration. Cloud-init is commonly used to automatically configure Linux machines when they first start, particularly in cloud and virtualized environments.
>
> In the output, Ubuntu is also generating **SSH host keys**. These identify the SSH server itself and allow clients to verify that they're connecting to the expected machine. The strange-looking `randomart` is simply a visual representation of a key's fingerprint.
>
> We'll let Ubuntu handle this automatically for now, but we'll encounter the idea of unique machine identity again when we create our additional VMs.

---

## Log In to Ubuntu Server

After the reboot finishes, Ubuntu should eventually display a login prompt:

![[ubuntu-server-first-login-terminal.png]]

The name before `login:` is the server's **hostname**.
Earlier, we configured this machine with:

```text
Hostname: docker-template
Username: dockeradmin
```

Enter the username:

```text
dockeradmin
```

Ubuntu will then ask for the password we created during installation.

```text
Password:
```

Type the password and press `Enter`.

> [!NOTE]
> Nothing will appear on the screen while typing your password — not even `*` characters. This is normal behavior in Linux terminals.

After successfully logging in:

![[ubuntu-server-first-login-terminal-succcessfull-login.png]]

we'll arrive at a command prompt similar to:

![[ubuntu-server-first-login-terminal-succcessfull-login-ready.png|503]]

We now have direct terminal access to our Ubuntu Server.

Next, we'll update Ubuntu and install **Docker Engine**.


---

# Install Docker Engine

We now have **Ubuntu Server** running inside our virtual machine.
But remember what we're preparing this machine for:

> **This machine will eventually become a Docker Swarm node.**

For that to happen, it needs to be able to run Docker containers.

We don't need **Docker Desktop** inside our Ubuntu Server VM. Instead, we'll install **Docker Engine** directly on Ubuntu.

---

## Update Ubuntu

![[update-software.png|170]]

Before installing Docker, update Ubuntu's package information:

```bash
sudo apt update
```

Then install any available updates:

```bash
sudo apt upgrade -y
```


> [!INFO]- Didn't We Just Install Ubuntu?
> Yes, but the Ubuntu ISO contains the package versions that were available when that installation image was created. Newer package versions and security updates may have been released since then.
>
> `apt update` checks what's currently available from Ubuntu's repositories, while `apt upgrade` installs newer versions of the packages already on our system.

---

## Add Docker's Repository

![[apt-checkmark.png|202]]

Ubuntu uses **APT (Advanced Package Tool)** to install and update software.

When we use:

```bash
sudo apt install <package>
```

APT doesn't search the internet for that software. It looks in a predefined list of **repositories** that Ubuntu already knows about.

**This is important for security.**

APT doesn't automatically trust any website or company on the internet as a source of software. If we want to install packages from a new **third-party repository**, such as Docker's, we first need to tell APT about that repository and give it a way to verify the packages it provides.

> [!INFO]- What Is a Third-Party Repository?
> A **third-party repository** is simply a software repository maintained by someone other than Ubuntu.
>
> In our case, **Docker** is the third party because we're choosing to get Docker Engine directly from Docker rather than from Ubuntu's repositories.
>
> [Ubuntu Community Help – Repositories](https://help.ubuntu.com/community/Repositories)

This introduces a few concepts that may look unfamiliar at first, such as **signing keys, certificates and repository configuration**. Don't worry about memorizing the commands. We'll go through what each one is doing as we use it.

For Docker, our goal is simply:
1. Tell APT **where Docker's repository is**.
2. Give APT a way to **verify packages coming from Docker**.
3. Install Docker using `apt` normally.

> [!INFO]- Why Does APT Verify Packages?
> Software repositories use **digital signatures** so APT can authenticate downloaded packages and repository information before trusting them. We'll see this in practice when we add Docker's signing key.
>
> [Debian Wiki – SecureAPT](https://wiki.debian.org/SecureApt)

> [!NOTE]
> Ubuntu also provides its own packaged version of Docker Engine. We're using Docker's repository because we're following **Docker's official installation method**.
>
> [Docker Docs – Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)


---

### Give APT a Way to Verify Docker

Before we add Docker's repository, we need a way for APT to verify that the packages it receives really came from Docker.

Docker does this using a **signing key**, which they publish on their website. We need to download that key and store it on our system.

To download it securely, Docker's instructions use two packages:

- **`ca-certificates`** - gives Ubuntu a collection of trusted **Certificate Authority (CA) certificates**. These are used to verify HTTPS connections, so when we connect to Docker's website, the system can check that it's communicating securely with the expected site.
  
- **`curl`** - is a command-line tool for **retrieving data from URLs**. We're about to use it to download Docker's signing key from their website.

Make sure both are available:

```bash
sudo apt install ca-certificates curl
```

> [!INFO]- Why Are We Installing These?
> These aren't parts of Docker. We need them because we're about to download Docker's signing key from its website.
>
> `ca-certificates` helps us establish a trusted HTTPS connection, while `curl` retrieves the key through that connection.
>
> They may already be installed. This command simply makes sure they're available before we continue.


---

### Add Docker's Signing Key

We've told ourselves that APT needs a way to verify that packages really came from Docker. This is where the **signing key** comes in.
Docker signs the packages it publishes. We download Docker's **public signing key** so that APT can check those signatures later.

**Alright, so..**
The next section takes information from the following page, and since docker regularly update their docs, please make sure you verify the instructions first: https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

With that in mind:
First, we need somewhere to store Docker's signing key.

Create the directory:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

This creates:

```text
/etc/apt/keyrings/
```

The `-d` tells `install` to create a **directory**, while `-m 0755` sets the directory's permissions.

> [!INFO]- What Does 0755 Mean?
> `0755` controls the permissions of the **directory we just created**. The owner can read, write and access it, while the group and everyone else can read and access it but cannot modify it.
>
> - `7` → owner: read + write + access
> - `5` → group: read + access
> - `5` → everyone else: read + access
>
> The leading `0` indicates that the permissions are written in octal notation.

Now we need to put Docker's signing key **inside that directory**.
Download the key:

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
```

This creates the file:

```text
/etc/apt/keyrings/docker.asc
```

Finally, make sure the **key file itself** can be read by APT:

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Here, `a+r` means **add (`+`) read (`r`) permission for everyone (`a`)**.

> [!NOTE]
> We're setting permissions on two different things:
>
> `0755` → permissions for the **`keyrings` directory**  
> `a+r` → adds read permission to the **`docker.asc` file**

We now have Docker's signing key stored on our machine and readable by APT.

**We haven't installed Docker yet.** We've simply given APT the key it will later use to verify packages coming from Docker.

---

### Verify the Signing Key

Before continuing, let's check that the key was created correctly:

```bash
ls -l /etc/apt/keyrings/docker.asc
```

We should see the `docker.asc` file along with its permissions, owner and size.
You should see the file listed, for example:

```text
-rw-r--r-- 1 root root ... /etc/apt/keyrings/docker.asc
```

This confirms that the key file exists and is readable.

> [!INFO]- Reading the Permissions
> `-rw-r--r--` describes the file's permissions. The owner (`root`) can **read and write** the file, while the group and everyone else can **read** it.
>
> That's what we want: administrators can modify the key, while APT can read it when verifying Docker's packages.


---

### Add Docker's Repository to APT

Earlier, we established that three things need to happen before we can install Docker:
1. Tell APT **where Docker's repository is**.
2. Give APT a way to **verify packages coming from Docker**.
3. Install Docker using `apt` normally.

We've now completed the second step.
APT has Docker's signing key stored here:

```text
/etc/apt/keyrings/docker.asc
```

That gives APT a way to verify Docker's repository.

Now we can go back to the **first step**:

> **Tell APT where Docker's repository is.**

Remember that APT doesn't search the internet when we ask it to install something. It looks through the **repositories it has been configured to use** and Docker's repository isn't in that list yet. So how do we add it?

To add it, we're going to create a small configuration file:

```text
/etc/apt/sources.list.d/docker.sources
```

This file will describe Docker's repository to APT. It will tell APT:
- **where** the repository is,
- which **Ubuntu version** it should use,
- which **CPU architecture** it should download packages for,
- and which **signing key** should be used to verify the repository.

Let's create it:

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

> [!TIP]- Connect to the VM Using SSH to copy/paste
> The configuration below contains several lines and special characters, which can be tedious to type manually in the virtual machine.
>
> To make things easier, you can connect to the Ubuntu VM using **SSH from your computer's normal terminal**. This lets you copy and paste commands normally while still executing them on the Ubuntu VM.
>
> First, find the VM's IP address:
>
> ```bash
> hostname -I
> ```
>
> Then, from a terminal on your **host computer**, connect using:
>
> ```bash
> ssh dockeradmin@<VM-IP>
> ```
>
> For example:
>
> ```bash
> ssh dockeradmin@172.16.108.130
> ```
>
> The first time you connect, SSH may ask whether you trust the host. Type `yes`, then enter the password for `dockeradmin`.
>
> Once you see:
>
> ```text
> dockeradmin@docker-template:~$
> ```
>
> you're controlling the Ubuntu VM through SSH. You can now paste the entire configuration block below instead of typing it manually.
>
> > [!NOTE]
> > The VM's IP address may change after a reboot because we're currently using DHCP. Always use the address shown by `hostname -I`.

There's quite a lot of syntax here. Don't worry about understanding the entire command yet.
For now, the important result is that we've created:

```text
/etc/apt/sources.list.d/docker.sources
```

and placed Docker's repository configuration inside it.

> [!INFO]- What Does This Configuration Say?
> **`Types: deb`**  
> Docker's repository provides Debian-style software packages, which Ubuntu can install using APT.
>
> **`URIs: https://download.docker.com/linux/ubuntu`**  
> This tells APT **where Docker's repository is located**.
>
> **`Suites: ...`**  
> This automatically determines which Ubuntu release we're running.
>
> **`Components: stable`**  
> This tells APT to use Docker's **stable** package channel.
>
> **`Architectures: ...`**  
> This automatically determines our machine's architecture, such as `arm64` or `amd64`.
>
> **`Signed-By: /etc/apt/keyrings/docker.asc`**  
> This tells APT to use the **signing key we downloaded earlier** when verifying Docker's repository.

Notice how the two parts now connect:

```text
Repository:
https://download.docker.com/linux/ubuntu

Verified using:
 /etc/apt/keyrings/docker.asc
```

APT now knows **where Docker's packages come from** and **how to verify that source**.

We still haven't installed Docker. 
We've simply finished configuring APT so that Docker's repository can be used as one of its software sources.

---

### Verify the Repository

We can check what was written to the file:

```bash
cat /etc/apt/sources.list.d/docker.sources
```

You should see the Docker repository configuration we just added.

---
### Update APT

APT now knows about Docker's repository, but it hasn't retrieved its list of available packages yet.

Run:

```bash
sudo apt update
```

![[update-docker-sudo-apt-update.png]]

This time, the output should include Docker's repository, for example:

```text
https://download.docker.com/linux/ubuntu
```

APT may also tell you that some of your **already-installed packages can be upgraded**:

```text
3 packages can be upgraded.
```

If so, install those updates:

```bash
sudo apt upgrade -y
```

We're now ready to actually install **Docker Engine**.

---

## Install Docker Engine

Install Docker Engine and its supporting components:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## Install Docker Engine

Install Docker Engine and its supporting components:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

![[install-docker-terminal-confirmation.png]]

Make sure you continue with `y` and `enter`.

> [!WARNING]- Troubleshooting: `NO_PUBKEY` or Unsupported Key File
> If `sudo apt update` fails with messages similar to:
>
> ```text
> The following signatures couldn't be verified because the public key is not available: NO_PUBKEY ...
>
> The key(s) in the keyring /etc/apt/keyrings/docker.asc are ignored as the file has an unsupported filetype.
> ```
>
> APT successfully **found Docker's repository**, but it couldn't use the signing key we downloaded earlier to verify it.
>
> Before changing anything, check what was actually saved as `docker.asc`:
>
> ```bash
> file /etc/apt/keyrings/docker.asc
> ```
>
> Then inspect the beginning of the file:
>
> ```bash
> head /etc/apt/keyrings/docker.asc
> ```
>
> A valid ASCII-armored signing key should begin with:
>
> ```text
> -----BEGIN PGP PUBLIC KEY BLOCK-----
> ```
>
> If it doesn't, the file we downloaded is probably **not actually Docker's public signing key**, even though a file named `docker.asc` exists.
>
> This is an important distinction: our earlier `ls -l` check proved that the **file existed and was readable**. It did **not** prove that the contents of the file were a valid signing key.
>
> Don't bypass APT's signature verification to make the error disappear. The verification is there to prevent APT from trusting packages it cannot authenticate.

This installs:

| Package | Purpose |
| --- | --- |
| `docker-ce` | Docker Engine |
| `docker-ce-cli` | The `docker` command-line interface |
| `containerd.io` | Container runtime used by Docker |
| `docker-buildx-plugin` | Extended Docker image building functionality |
| `docker-compose-plugin` | Docker Compose support |

Once installation finishes, check whether Docker is running:

```bash
sudo systemctl status docker
```

![[install-docker-terminal-confirmation-status.png]]

Look for:

![[install-docker-terminal-confirmation-status-running.png]]

Press:

```text
q
```

to exit the status screen.

> [!NOTE]
> On Ubuntu, Docker normally starts automatically after installation and is configured to start when the system boots.

---

# Test Docker

Docker is installed, but before we use this VM as our clean starting point, let's verify that Docker actually works.

Run:

```bash
sudo docker run hello-world
```

Docker will:

1. Look for the `hello-world` image locally.
2. Download it if it isn't available.
3. Create a container from the image.
4. Run the container.

If everything is working, you should see:

![[install-docker-terminal-hello-world-example.png]]

The test container prints its message and then exits.
We've now confirmed that our Ubuntu Server VM can successfully run Docker containers.

---

# Run Docker Without sudo

You may have noticed that we've been using:

```bash
sudo docker ...
```

By default, our normal user doesn't have permission to communicate with the Docker daemon.
For our lab environment, we'll add our user to the `docker` group:

```bash
sudo usermod -aG docker $USER
```

> [!INFO]- What Does This Mean?  
> Let's break the command down:  
>  
> **`sudo`**  
> Runs the command with administrator privileges. Changing a user's group membership requires elevated permissions.  
>  
> **`usermod`**  
> Stands for **user modify**. It's a Linux command used to change properties of an existing user account, such as which groups the user belongs to.  
>  
> **`-a`**  
> Means **append**. It adds the user to another group **without removing them from their existing groups**.  
>  
> **`-G`**  
> Specifies the **supplementary group or groups** we want the user to belong to.  
>  
> **`docker`**  
> This is the group we're adding the user to. Members of this group are allowed to communicate with the Docker daemon without using `sudo` for each Docker command.  
>  
> **`$USER`**  
> `$USER` is an environment variable containing the username of the currently logged-in user.  
>  
> We can see its value with:  
>  
> ```bash  
> echo $USER  
> ```  
>  
> In our VM, this should return:  
>  
> ```text  
> dockeradmin  
> ```  
>  
> So our command effectively means:  
>  
> > **Modify `dockeradmin` and append them to the `docker` group.**  
>  
> The combination **`-aG`** is especially important. `-G` specifies the supplementary groups, while `-a` tells `usermod` to **append** the new group rather than replacing the user's existing supplementary group memberships.

The new group membership won't apply to our current login session immediately.
Log out and back in, or restart the virtual machine.
Then verify that Docker works without `sudo`:

```bash
docker run hello-world
```

> [!WARNING]
> Membership in the `docker` group grants the user **root-level privileges** through Docker.
>
> We're doing this intentionally for our local learning environment.

> [!INFO]- What About Rootless Docker?
> Docker can also be configured to run without giving the user root-level access through the `docker` group. This is known as **Rootless mode**. It's worth exploring when learning more about Docker security, but isn't necessary for our Docker Swarm lab.

---

# Verify Our Docker Host

Before preserving this machine, let's perform two quick checks.

Check the installed Docker version:

```bash
docker --version
```

Then check the machine's network interfaces:

```bash
ip addr
```

We don't need to configure Docker Swarm networking yet. For now, we're simply confirming that the machine has working networking and Docker Engine.

This machine can now run Docker containers independently.

It isn't a **Docker Swarm node** yet. A machine becomes part of our Swarm later, when we initialize the cluster and join our machines together.

For now, we have exactly what we wanted:

> **A clean Ubuntu Server with Docker Engine installed and working.**

---

# Save the Clean Docker Host

Eventually, our Docker Swarm will consist of three machines:

```text
node1
node2
node3
```

We *could* install Ubuntu Server, configure it and install Docker separately on all three machines.

But we'd mostly be repeating exactly the same work.

Instead, we've prepared one machine containing the configuration our future nodes will have in common: `Ubuntu Server, Docker, User config, and Working Network`

We'll use this machine as the **starting point** for our other virtual machines.

> [!TIP]
> This is a good point to create a snapshot of the VM.
>
> A useful name could be:
>
> ```text
> Docker Installed - Clean Ubuntu
> ```
>
> This gives us a known working state that we can return to if something goes wrong later.

---

# What Did We Learn?

We now know that:
- Our Docker Swarm will consist of multiple **nodes**.
- A node is a machine participating in a cluster.
- We'll create our nodes using virtual machines.
- We'll use **Ubuntu Server** as the operating system for those nodes.
- Ubuntu Server doesn't require a graphical desktop.
- Intel and AMD computers generally use **AMD64**.
- Apple Silicon and other 64-bit Arm computers use **ARM64**.
- Ubuntu Server is downloaded as an **ISO**.
- An ISO acts as installation media rather than an already-installed virtual machine.
- The same Ubuntu Server ISO can be used to create several independent VMs.

---

# What's Next?

We have the operating system image we need.

If you came from Docker Module 03:
	Next, we'll use it to create our first virtual machine and install **Ubuntu Server**.
	Once we understand the process, we can create the additional machines needed for our
	Docker Swarm.
	Navigate back to: [[Docker - Module 03]]