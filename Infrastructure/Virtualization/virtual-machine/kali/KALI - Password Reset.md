---
icon: LiKey
---
# Overview

Forgetting the password to a virtual machine doesn't necessarily mean we need to reinstall the entire operating system.

Because we control the virtual machine and its boot process, we can temporarily start Kali in a way that gives us access to the system before the normal login screen appears.

From there, we can reset the password of a local user.

> [!IMPORTANT]
> This guide assumes you have legitimate administrative access to your own Kali virtual machine.
>
> It also assumes the Kali installation isn't protected by disk encryption that prevents access to the filesystem during boot.

---

# How Can We Reset a Password Without Logging In?

Imagine we've created a Kali VM and haven't used it for several months.
We start the VM, reach the login screen and realize:
**We don't remember the password.**

Normally, Kali follows a startup process similar to:

```text
VM Starts up - GRUB - Linux kernel - System init - Login
```

The interesting part is that **GRUB runs before Kali has fully started**.

If we temporarily modify how Linux starts, we can tell the system to open a root shell instead of continuing through the normal initialization process.
From that shell, we can change the user's password.

> [!NOTE]
> We're not discovering the old password.
> We're using administrative access to the machine to **replace it with a new one**.

---

# Restart the Virtual Machine

Start by restarting the Kali virtual machine.

![[kali-vm-password-reset-restart-vm.png|325]]

We need to interact with the bootloader before Kali reaches its normal login screen.
When the **GNU GRUB** menu appears, select the Kali Linux boot entry.

---

# Edit the GRUB Boot Entry

Instead of starting Kali normally, press:

```text
e
```

This tells GRUB that we want to temporarily **edit the selected boot entry**.

![[kali-vm-gnu-linux-click-e.png]]

> [!NOTE]
> We're modifying the boot instructions for this startup.

---

# Find the Linux Boot Command

GRUB will display the commands used to start Kali.

Look for the line beginning with:

```text
linux
```

It will reference the Linux kernel and normally contain something similar to:

```text
/boot/vmlinuz-...
```

![[kali-vm-arrow-navigate-linux-boot-vmlinuz.png]]

> [!warning]
> The green arrow is pointing at the wrong location. It should be pointing at linux

This line contains parameters that GRUB passes to the Linux kernel when the system starts.
Navigate to the end of this line.

---

# Change the Init Process

Normally, Linux starts its regular initialization system after loading the kernel.
For password recovery, we'll temporarily tell Linux to start **Bash** instead.

Add:

```text
init=/bin/bash
```

![[kali-vm-init-bin-bash.png]]

This changes what happens during this particular boot.

> [!IMPORTANT]
> This is why the technique works.
>
> We're temporarily interrupting the normal startup sequence and asking Linux to launch a shell directly.

---

# Boot With the Modified Configuration

Start Kali using the temporarily modified boot entry.
Instead of reaching the normal graphical login screen, we should eventually reach a **root shell**.

![[kali-vm-root-terminal.png]]

At this point, we have administrative access to the operating system.
However, there's another problem we need to solve before changing the password.

---

# Make the Filesystem Writable

During this type of recovery boot, the root filesystem may not be writable in the way we need.

Changing a password modifies files on the system, so we need write access.

Remount the root filesystem as **read/write**:

```bash
mount -o remount,rw /
```

![[kali-vm-mount-o-remount-rw.png]]

The important part is:

```text
rw = read/write
```

This allows changes to be written to the filesystem.

> [!NOTE]
> Without write access, we could potentially inspect files but wouldn't be able to successfully save the new password information.

---

# Find the Username

We now need to know which user's password we're resetting.
If you've forgotten the username as well, look inside:

```bash
ls /home
```

![[kali-vm-ls-home-confirm-username.png]]

Linux normally creates a home directory for regular users.

For example:

```text
/home/kali
```

would indicate that a user named:

```text
kali
```

exists on the system.

> [!TIP]
> `/home` is a useful place to check for normal local user accounts, although its contents should not be treated as a complete list of every account on a Linux system.

---

# Reset the Password

Once we know the username, use:

```bash
passwd <username>
```

For example:

```bash
passwd kali
```

![[kali-vm-hashtag-passwd-username-new-password.png]]

Enter the new password when prompted and enter it again to confirm.
The password won't normally appear on screen while you're typing it.

That's expected.

If successful, Kali should confirm that the password was updated.

> [!SUCCESS]
> The old password hasn't been recovered.
>
> The account now has a **new password**.

---

# Continue the Normal Boot Process

We started Bash instead of Kali's normal initialization process.
Now that the password has been changed, we want the system to continue booting normally.

Run:

```bash
exec /sbin/init
```

![[kali-vm-exec-sbin-init.png]]

`exec` replaces our current shell process with the normal initialization process.

Kali should now continue through its normal startup process.
If necessary, restart the VM normally afterward.

---

# Log In With the New Password

Once Kali reaches its normal login screen, select your user and enter the **new password**.

You should now have access to the system again.

The temporary GRUB modification doesn't need to become our normal boot configuration. The next normal startup can use the ordinary GRUB entry again.

> [!SUCCESS]
> The Kali user's password has been reset without reinstalling the virtual machine.

> [!WARNING]
> A login password alone doesn't necessarily protect data from someone who has sufficient control over the machine's boot process.
>
> This is one reason **disk encryption** can be important. Encryption protects the underlying data rather than relying only on the operating system's login screen.

---

# What Did We Learn?

A forgotten Kali password doesn't necessarily require reinstalling the virtual machine.

By temporarily modifying the Linux boot parameters through GRUB, we can start a root shell before the normal login process. We then make the filesystem writable, identify the user and use `passwd` to assign a new password.

> **We used administrative control of the VM to replace our old password.**

---

# Other VM Configuration Worth Exploring

Once password recovery is understood, there are several other useful parts of the virtual machine worth understanding:

1. [[VMware Configuration - Network Settings]]
2. [[VMware Configuration - Encryption]] 
3. [[VMware Configuration - Disk Size]]
4. [[VMware Configuration - Performance]] 
5. [[KALI - Password Reset]] - CURRENT
6. [[VMware Configuration - Snapshot]] 