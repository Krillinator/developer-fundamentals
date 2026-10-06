---
icon: LiDownload
---
# Downloading Kali Linux for Virtualbox

![[virtualbox-logo.avif]]

Now that we have a hypervisor installed, we need an operating system to run inside our virtual machine.

For pentesting, we'll use **Kali Linux**.

![[Pasted image 20260918133726.png|333]]

Kali provides several different types of downloads depending on how we want to use it.

Instead of installing Kali from scratch, we're going to use one of Kali's **pre-built virtual machines**.

This gives us a Kali system that has already been prepared for virtualization and can be opened directly in software such as VirtualBox or VMware.

> [!TIP]
> If you're following this course on **Windows with VirtualBox**, download the **VirtualBox pre-built VM**.

---

# Download the Virtual Machine

Visit the official Kali Linux download page:

https://www.kali.org/get-kali/#kali-virtual-machines

Scroll to:

**Pre-built Virtual Machines**

You'll find downloads for several virtualization platforms, including:

- VMware
- VirtualBox
- Hyper-V
- QEMU

![[vmware-virtualbox-kali-vm-download.png]]

For this guide, select:

**VirtualBox**

Kali's pre-built VirtualBox image already has the operating system installed and configured for VirtualBox.

> [!IMPORTANT]
> Always download Kali images from the **official Kali website**.
>
> Kali specifically recommends against downloading its images from unofficial sources.
>
> Read more:
> https://www.kali.org/docs/introduction/download-official-kali-linux-images/

---

# Which Architecture?

You may encounter architecture names such as:

**amd64 / x86_64**

and

**ARM64**

These describe the CPU architecture the operating system was built for.

For most ordinary **Windows PCs using Intel or AMD processors**, use:

**amd64 / x86_64**

Despite the name `amd64`, this architecture is used by modern **Intel and AMD 64-bit processors**.

> [!TIP] Windows
> If you're using a normal Intel or AMD Windows computer with VirtualBox, the **amd64 VirtualBox image** is generally what you're looking for.

ARM64 is intended for Arm-based systems.

This distinction is particularly important with devices such as Apple Silicon Macs, which use the Arm architecture.

---

# Download, Torrent, Docs and Sum

Next to the VirtualBox image, Kali provides several options.

You may see something similar to:

```text
↓    torrent    docs    sum
```

They each have a different purpose.

| Option | Purpose |
| --- | --- |
| **↓ Download** | Download the VM directly |
| **torrent** | Download the VM using BitTorrent |
| **docs** | Instructions for using the VM |
| **sum** | SHA256 checksum used to verify the download |

For most people, the normal **Download** button is the easiest option.

---

## Torrent

**Torrent** provides an alternative way to download the same image using the BitTorrent protocol.

Instead of downloading the entire file directly from one web server, pieces of the file can be downloaded from multiple peers.

You need a BitTorrent client to use the `.torrent` file.

For this course, you don't need to use the torrent option.

---

## Docs

**Docs** takes you to Kali's documentation for that particular virtual machine.

For VirtualBox:

https://www.kali.org/docs/virtualization/import-premade-virtualbox/

This is useful if you want to see Kali's official instructions for extracting and opening the pre-built VM.

---

## Sum

**Sum** refers to the image's **SHA256 checksum**.

A checksum acts like a fingerprint for a file.

Kali publishes the expected SHA256 value of the download. After downloading the file, we can calculate its SHA256 value ourselves and compare the two.

```text
Kali's published SHA256
             =
SHA256 of our downloaded file
```

If they match, we have evidence that the file we downloaded is identical to the expected file.

This is particularly relevant for an operating system such as Kali Linux.

> [!IMPORTANT]
> Kali recommends verifying downloaded images before using them.
>
> Learn more:
> https://www.kali.org/docs/introduction/download-images-securely/

---

# Pre-Built VM vs ISO

You may notice that Kali also provides **Installer Images** as `.iso` files.

An ISO and a pre-built VM aren't the same thing.

### ISO

An ISO is essentially installation media.

We would create an empty virtual machine, attach the ISO and then go through the Kali installation ourselves.

```text
ISO
↓
Create empty VM
↓
Boot installer
↓
Install Kali
↓
Configure VM
```

### Pre-Built Virtual Machine

A pre-built VM has already gone through much of that process for us.

```text
Pre-built VM
↓
Extract
↓
Open in VirtualBox
↓
Run Kali
```

For this course, the **pre-built VM is the easier option**.

> [!SUMMARY]
> **ISO** → Install Kali yourself
>
> **Pre-built VM** → Kali is already installed

---

# Extract the Download

The Kali VirtualBox VM is distributed as a compressed archive.

Kali's official instructions use **7-Zip** to extract it.

On Windows, you can download 7-Zip here:

https://www.7-zip.org/

Extract the downloaded Kali archive.

After extraction, you'll find the files that make up the virtual machine.

Two particularly important files are:

```text
.vbox
.vdi
```

These files have very different jobs.

---

# What Is a VBOX File?

A `.vbox` file is the **VirtualBox configuration file** for a virtual machine.

It describes how VirtualBox should configure the VM.

It contains information about the virtual machine rather than containing the operating system itself.

Think of it as:

> **The instructions describing the virtual machine.**

When we open the pre-built Kali VM, this is the file we're interested in.

```text
kali-linux-....vbox
```

---

# What Is a VDI File?

A `.vdi` file is a **Virtual Disk Image**.

This acts as the virtual machine's **hard drive or SSD**.

The Kali operating system, files, applications and other data stored on the virtual machine live on this virtual disk.

Think of it as:

> **The virtual computer's storage drive.**

```text
Kali VM

.vbox
└── VM configuration

.vdi
└── Virtual hard disk
    └── Kali Linux
    └── Applications
    └── Files
```

Oracle documentation:

https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/vdidetails.html

> [!NOTE]
> Don't confuse the two.
>
> **VBOX = describes the virtual machine**
>
> **VDI = acts as its virtual hard disk**

---

# Open Kali in VirtualBox

Once the archive has been extracted, open **VirtualBox**.

Select:

**Open**

![[virtualbox-instructions-open-vm-step-1.png]]

Navigate to the extracted Kali directory.

You'll likely see both the `.vbox` and `.vdi` files.

Select the:

**`.vbox` file**

![[virtualbox-instructions-open-vm-step-2.png]]

VirtualBox will read the configuration and add the pre-built Kali virtual machine to VirtualBox Manager.

> [!TIP]
> You don't need to manually create a new VM and attach the `.vdi`.
>
> Kali already provides the `.vbox` configuration for us, so we can simply open it.

Kali's official import guide follows this same process:

https://www.kali.org/docs/virtualization/import-premade-virtualbox/

---

# Before Starting the VM

Before immediately starting Kali, it's worth checking the virtual machine's settings.

The pre-built image already contains sensible defaults, but the VM still consumes resources from your physical computer.

Pay particular attention to:

- **Memory (RAM)**
- **CPU cores**
- **Virtual disk**
- **Network configuration**

Remember:

```text
Your Windows PC
│
├── Windows
│
└── VirtualBox
    └── Kali VM
        ├── Virtual CPU
        ├── Virtual RAM
        ├── Virtual Disk (.vdi)
        └── Virtual Network
```

The CPU and RAM assigned to Kali ultimately come from your **physical computer**.

We'll explore these settings separately rather than changing things blindly.

---

# Default Login

Kali's current pre-built virtual machines use the default credentials:

```text
Username: kali
Password: kali
```

These credentials are documented by Kali for its pre-built VMware and VirtualBox images.

> [!WARNING]
> These are well-known default credentials.
>
> If you're using the VM for anything beyond an isolated learning environment, change the password.

---

# What Did We Learn?

We now know that:

- Kali provides pre-built images specifically for VirtualBox and VMware.
- **VirtualBox** is our primary choice for a typical Windows computer in this guide.
- Most Intel/AMD Windows PCs use the **amd64 / x86_64** architecture.
- **Download** retrieves the image directly.
- **Torrent** provides an alternative BitTorrent download.
- **Docs** opens Kali's official instructions.
- **Sum** provides the SHA256 checksum used to verify the download.
- An **ISO** is installation media, while a **pre-built VM** already contains an installed system.
- A `.vbox` file contains the VirtualBox VM configuration.
- A `.vdi` file acts as the VM's virtual hard drive.
- Kali's pre-built VirtualBox VM can be added by opening its `.vbox` file.

---

# What's Next?

Kali is downloaded and VirtualBox knows about our virtual machine.

Next, we'll actually **install and configure Kali Linux**.

We'll go through the remaining setup, including:

- Starting the Kali installer
- Language, keyboard and regional settings
- User and account configuration
- Disk configuration
- Software selection
- **GRUB bootloader**
- Completing the installation
- Booting into Kali for the first time
