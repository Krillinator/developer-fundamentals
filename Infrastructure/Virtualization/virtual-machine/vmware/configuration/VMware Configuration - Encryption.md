---
icon: LiHash
---
# Overview

Our Kali virtual machine contains its own operating system, applications, configuration and files.
If some of that information is sensitive, VMware allows us to **encrypt the virtual machine**.

Encryption protects the VM's data so that someone cannot simply access its files without the required encryption password.

---

# Why Encrypt a Virtual Machine?

Imagine someone gets access to the files that make up our Kali VM.

A normal Kali login password protects access through the operating system's login screen, but the VM itself consists of files stored on our physical computer.

Encryption adds another layer of protection.

> [!NOTE]
> **Kali password** and **VM encryption password** are different things.
>
> The Kali password protects your user account inside Linux.
>
> VMware encryption protects the virtual machine itself.

---

# Configure Encryption

Open the virtual machine's settings.

![[vm-encryption-navigate.png]]

Navigate to: **Encryption**

![[vm-encryption-navigate-encryption.png]]

VMware allows us to configure encryption and protect the VM with a password.

![[vm-encryption.png|497]]

Choose a strong password that you can safely store and recover later.

> [!WARNING]
> Don't treat this password like an ordinary Kali login password.
>
> If you forget your Kali password, we can reset it using recovery methods. Losing the password required to decrypt an encrypted VM can prevent access to the protected VM data.

---

# Encryption and Performance

Encryption requires VMware to encrypt and decrypt protected data as the VM operates.

Modern computers generally handle this efficiently, but encryption still involves additional work compared with storing the same data unencrypted.

For a normal learning VM, encryption isn't something we necessarily need to enable. It's a feature worth understanding and using when the VM contains information that actually needs protection.

> [!TIP]
> Don't enable encryption simply because the option exists.
>
> Understand **what you're protecting, why you're protecting it and how you'll securely retain the encryption password**.

---

# What Did We Learn?

VMware encryption protects data belonging to the **virtual machine itself**, while the Kali login password protects access to a **user account inside Kali**.

Encryption can provide useful protection for sensitive virtual machines, but it also makes securely managing the encryption password important.

---

# Other VM Configuration Worth Exploring

Once encryption is understood, there are several other useful parts of the virtual machine worth understanding:

1. [[VMware Configuration - Network Settings]]
2. [[VMware Configuration - Encryption]]  - CURRENT
3. [[VMware Configuration - Disk Size]]  
4. [[VMware Configuration - Performance]]
5. [[KALI - Password Reset]]
6. [[VMware Configuration - Snapshot]]