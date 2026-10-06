---
icon: LiHardDrive
---
# Overview

When we created our Kali virtual machine, VMware created a **virtual disk** for it.

To Kali, this behaves much like a normal SSD or hard drive. The difference is that the storage is provided virtually by VMware and ultimately uses space on our physical computer.

As we install applications, download files and work inside Kali, we may eventually need more space.

---

# What Happens When the Disk Gets Full?

Imagine we originally created our Kali VM with:

```text
Virtual Disk: 20 GB
```

After using Kali for a while, we install more tools and store more files.
Eventually, 20 GB might no longer be enough.

We don't necessarily need to create an entirely new VM. 
VMware allows us to **expand the virtual disk**.

> [!IMPORTANT]
> Expanding a virtual disk is generally a **one-way operation** in VMware.
>
> Don't increase it unnecessarily. Give the VM additional storage when you actually need it.

---

# Check the Disk Configuration

Open the virtual machine's settings.

![[vm-disk-size-navigate.png]]

Navigate to the virtual machine's storage device.
For our Kali VM, this may appear as:
**Hard Disk (NVMe)**

![[vm-disk-size-navigate-hard-disk-nvme.png]]

The exact name can differ depending on how the VM was created.

---

# Expand the Virtual Disk

Open the hard disk settings.

Here we can see the current maximum size of the virtual disk and increase it when necessary.

![[vm-disk-size.png]]

For example:

```text
Current size:    20 GB
New size:        40 GB
```

This increases the amount of virtual storage VMware makes available to the VM.

> [!NOTE]
> Increasing the virtual disk's maximum size doesn't necessarily allocate all of that physical storage immediately.
>
> Virtual disks can grow as data is actually written to them, depending on how the disk was configured.

---

# Snapshots Can Prevent Disk Changes

VMware may prevent you from resizing the virtual disk while the VM has snapshots.

![[vm-disk-size-navigate-hard-disk-requires-snapshot-removal.png]]

This happens because snapshots depend on the existing virtual disk structure and its history.

If VMware requires snapshots to be removed before resizing, consider whether you actually need those restore points before deleting them.

> [!WARNING]
> Don't delete snapshots simply to get past the warning without considering what they contain.
>
> If those snapshots represent important restore points, decide whether you're comfortable losing them before continuing.

See: [VMware Configuration - Snapshot](<VMware Configuration - Snapshot>)

---

# VMware Disk Size vs Kali Disk Size

There is an important distinction when expanding storage.

VMware controls the **virtual disk**, while Kali controls the **partitions and filesystems inside that disk**.

If we expand the VMware disk from 20 GB to 40 GB, Kali may initially still have a partition/filesystem using only the original space.

> [!IMPORTANT]
> **Making the virtual disk larger and making Kali use the additional space are related, but they aren't always the same step.**

How the partition and filesystem should be expanded depends on how Kali's disk was originally configured, so always inspect the current disk layout before modifying partitions.

---

# What Did We Learn?

VMware gives our virtual machine a **virtual disk** that behaves like storage hardware from Kali's perspective.

If the VM needs more storage, VMware can increase the maximum size of that virtual disk. However, Kali may still need its partition or filesystem expanded before the operating system can actually use the additional space.

Snapshots can also prevent VMware from resizing a virtual disk and may need to be dealt with first.

The important distinction is:

> **VMware controls the size of the virtual disk.**
> **Kali controls how the space inside that disk is partitioned and used.**

---

# Other VM Configuration Worth Exploring

Once disk size is understood, there are several other useful parts of the virtual machine worth understanding:

1. [[VMware Configuration - Network Settings]]
2. [[VMware Configuration - Encryption]] 
3. [[VMware Configuration - Disk Size]]  - CURRENT
4. [[VMware Configuration - Performance]]
5. [[KALI - Password Reset]]
6. [[VMware Configuration - Snapshot]]