---
icon: LiSave
---
# Overview

Once we have a working virtual machine, we're probably going to start changing it.

We might install software, change network settings, experiment with system configuration or intentionally break something while learning.

But what happens if we want to go back?
This is where **snapshots** become useful.

---

# What Is a Snapshot?

Imagine you've just finished installing and configuring Kali Linux.
Everything works.

Before experimenting with the system, we create a **snapshot**.
The snapshot gives VMware a point that we can return to later.

If our experiments cause problems, we don't necessarily have to reinstall Kali from scratch. We can restore the VM to the state represented by the snapshot.

![[vm-snapshot-explanation.png]]

> [!TIP]
> Think of a snapshot as a **checkpoint for your virtual machine**.
>
> Get the VM working → create a snapshot → experiment → restore the snapshot if necessary.

---

# Why Are Snapshots Useful?

Snapshots are particularly valuable in **development, testing, cybersecurity labs and learning environments** because these are environments where we frequently make changes.

For example, we might want to:
- Install unfamiliar software
- Experiment with Linux configuration
- Change networking
- Test an application update
- Modify system files
- Practice administrative tasks
- Intentionally break something to understand how it works

Instead of being afraid of damaging the environment, we can create a known working point beforehand.

> [!EXAMPLE]
> We've just completed a fresh Kali installation.
>
> Before making further configuration changes, this is an excellent time to create our **first snapshot**.

---

# Create a Snapshot in VMware

Start by opening the virtual machine in VMware.
Navigate to VMware's **Snapshots** controls.

![[vm-snapshot-vmware-setup-navigate.png]]

Choose the option to create a new snapshot.
Before creating it, give the snapshot a descriptive name.
For example:

```text
Fresh Kali Installation
```

You can also add a description explaining what state the machine is currently in.

![[vm-snapshot-vmware-setup-showcase-fresh-install.png]]

Good snapshot names describe **why that state matters**.

For example:

```text
Fresh Kali Installation
Before Network Changes
Before Docker Installation
Working Configuration
```

This becomes much more useful than names such as:

```text
Snapshot 1
Snapshot 2
Test
New
```

---

# The Snapshot Is Created

Once VMware finishes creating the snapshot, it becomes a restore point for the virtual machine.

![[vm-snapshot-vmware-finalized.png|297]]

We can now continue using Kali normally.


---

# Restore a Snapshot

Suppose we've changed the VM and now want to return to our **Fresh Kali Installation** state.
Open the snapshot manager and select the snapshot we want to restore.

![[vm-snapshot-vmware-restore.png]]

Restoring the snapshot moves the virtual machine back to that earlier state.

This can affect much more than settings. Files, installed applications and other changes made inside the VM after that snapshot may also be lost.

> [!WARNING]
> Restoring a snapshot is not simply changing a VMware setting.
>
> You're asking VMware to return the **virtual machine itself** to an earlier state.

---

# Be Careful With Current Changes

When restoring an older snapshot, VMware may warn that the current state contains changes that will be discarded.

![[vm-snapshot-vmware-warning.png|325]]

Take this warning seriously.

Imagine we created this snapshot on Monday:

```text
Monday
> Fresh Kali Snapshot

Tuesday
+ Installed applications

Wednesday
+ Created files

Thursday
+ Changed configuration
```

If we restore the Monday snapshot, changes made afterward may no longer exist in the restored state.

Before restoring, ask yourself:

> **Is there anything in my current VM that I still need?**

If there is, save it somewhere appropriate or consider creating another snapshot before reverting.

---

# Snapshot vs Backup

A snapshot is extremely useful, but it shouldn't be confused with a proper **backup**.

A snapshot is closely connected to the virtual machine and its virtual disks. Its main purpose is to let us return that VM to an earlier state.

A backup is intended to preserve data independently so that it can be recovered if the original environment is lost or damaged.

For our learning environment, snapshots are perfect for creating checkpoints before experiments.

For important files or professional environments, snapshots should not be treated as a replacement for a proper backup strategy.


---

# What Did We Learn?

A VMware snapshot captures a **restorable point in the state of a virtual machine**.

This allows us to experiment with our VM while maintaining useful checkpoints that we can return to if something goes wrong.

Snapshots are especially valuable for local development and learning because they make experimentation much less expensive. Instead of reinstalling and rebuilding an environment every time we break something, we can return to a known working state.

The important thing to remember is:

> **Restoring a snapshot can discard changes made after that snapshot.**

Always consider whether the current state contains anything important before reverting.

---

# Other VM Configuration Worth Exploring

Once snapshots are configured, there are several other useful parts of the virtual machine worth understanding:

1. [[VMware Configuration - Network Settings]]
2. [[VMware Configuration - Encryption]] 
3. [[VMware Configuration - Disk Size]]
4. [[VMware Configuration - Performance]]
5. [[KALI - Password Reset]]
6. [[VMware Configuration - Snapshot]] - CURRENT