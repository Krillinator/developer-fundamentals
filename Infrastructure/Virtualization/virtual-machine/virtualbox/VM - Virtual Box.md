---
icon: LiBox
---
# VirtualBox

![[virtualbox-logo.avif]]

**VirtualBox** is another application we can use to create and run virtual machines on our computer.

It is developed by **Oracle** and works on several operating systems, including:
- Windows
- macOS
- Linux

Just like VMware Fusion and VMware Workstation, VirtualBox acts as a **hypervisor**.
A hypervisor allows us to create virtual computers that use resources from our physical computer.

Each virtual machine can have its own operating system, network configuration and Docker installation.

---

# VirtualBox or VMware?

Which one you should use depends somewhat on your computer.

| System | Recommended |
| --- | --- |
| Windows (Intel / AMD) | **VirtualBox or VMware Workstation** |
| Linux (Intel / AMD) | **VirtualBox or VMware Workstation** |
| Intel Mac | **VirtualBox or VMware Fusion** |
| Apple Silicon Mac | **VMware Fusion recommended** |

VirtualBox is particularly useful on **Windows** because it's straightforward, widely used and doesn't require creating an account before downloading it.

> [!TIP]
> **Using Windows?**
>
> VirtualBox is a great choice for following the virtual machine labs in this course.

---

# What About Apple Silicon?

VirtualBox now supports Macs using **Apple Silicon**, including M-series processors.

However, there is an important limitation.
Apple Silicon uses the **Arm64** architecture.
When VirtualBox is running on an Arm-based host, the virtual machines must also use an **Arm-based operating system**.

VirtualBox also has some additional feature limitations when running on Arm.

For this reason, we'll use **VMware Fusion** as our recommended option for Apple Silicon Macs.
[[VM - VMware]]

> [!WARNING] Apple Silicon
> VirtualBox does work on modern M-series Macs, but its Arm support has additional limitations.
>
> If you're using an **M1, M2, M3, M4 or later Apple Silicon Mac**, use **VMware Fusion** for this course unless you specifically want to experiment with VirtualBox.

---

# Is VirtualBox Free?

Yes.
The main **VirtualBox platform package** is open-source software distributed under the **GNU General Public License (GPL)**.

This means we can download VirtualBox itself without purchasing a license.

> [!NOTE]- What About the Extension Pack?
> You may also see something called the **VirtualBox Extension Pack** on the download page.
>
> This is separate from the main VirtualBox application and has its **own license**.
>
> We don't need the Extension Pack for our basic virtual machine and Docker Swarm labs.

For our purposes:

> **Download VirtualBox itself. Ignore the Extension Pack for now.**

---

# Download VirtualBox

No account required, visit: https://www.virtualbox.org/wiki/Downloads

You'll see a section called: **VirtualBox Platform Packages**
Choose the package matching the operating system of your **physical computer**.

> [!IMPORTANT]- What Does "Host" Mean?
> The **host** is your real computer.
>
> The **guest** is the operating system running inside the virtual machine.
>
> If your physical computer runs Windows, choose **Windows hosts** even if you're planning to install Linux inside the virtual machine.

---

# Windows Installation

If you're using Windows, select:

**Windows hosts**
This downloads the VirtualBox installer.

Open the downloaded installer.

Follow the installation wizard and keep the default options unless you have a reason to change them.

During installation, Windows may warn you that your network connection could temporarily be interrupted while VirtualBox installs its networking components.
This is expected.
Complete the installation and open **Oracle VirtualBox**.

> [!SUCCESS]
> VirtualBox is now installed on Windows.


---

# Verify the Installation

Open **VirtualBox**.
You should see the VirtualBox Manager.

At this point, VirtualBox is installed, but we haven't actually created a virtual machine yet.
VirtualBox itself is **not** the virtual machine.
It's the software we'll use to create and manage them.

---

# Basic and Expert Mode

When creating a new virtual machine, VirtualBox can present its settings in different ways.

![[virtualbox-basic-vs-expert-mode.png]]

You may encounter two modes:

- **Basic Mode**
- **Expert Mode**

They create the same kind of virtual machine. The difference is mainly **how the configuration options are presented**.

---

## Basic Mode

**Basic Mode** guides you through creating a virtual machine step by step.

Instead of showing everything at once, VirtualBox separates the configuration into stages.

This is useful when you're new to virtual machines because you can focus on one decision at a time.

Typical settings include:

- Virtual machine name
- Operating system
- CPU
- Memory
- Virtual hard disk

> [!TIP]
> If this is your first time using VirtualBox, **Basic Mode is recommended**.

---

## Expert Mode

**Expert Mode** gives you more control over the virtual machine during creation.

It exposes additional configuration options upfront, such as:
- CPU and memory allocation
- Virtual hard disk settings
- Operating system configuration
- Unattended installation options
- Additional hardware and installation settings

This is useful when you already know how you want the VM configured and don't need the guided process.

> [!NOTE]
> Expert Mode doesn't create a more powerful VM. It simply gives you **more configuration options upfront** and lets you configure the machine more quickly.

Instead of walking through each setting individually, you can configure several options before creating the VM.

This can be faster once you're comfortable with VirtualBox.

---

# What Did We Learn?

We now know that:

- VirtualBox is a desktop hypervisor developed by Oracle.
- It can create multiple virtual machines on one physical computer.
- VirtualBox is a good option for Windows and Linux computers.
- The main VirtualBox platform package is free and open source.
- No account is required to download VirtualBox.
- The download must match the architecture and operating system of the **host** computer.
- VirtualBox supports Apple Silicon, but Arm hosts have additional limitations.
- VMware Fusion is our recommended alternative for Apple Silicon Macs.

---

# What's Next?

We now have a hypervisor, but we still don't have a virtual machine.

For KALI setup (pentesting) check out: [[VM - Virtualbox - Download Kali]]
