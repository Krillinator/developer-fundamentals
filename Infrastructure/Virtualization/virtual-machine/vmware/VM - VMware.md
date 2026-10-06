---
icon: LiBox
---
# VMware

![[vmware-logo.webp|173]]

Before creating our virtual machines, let's briefly look at the software we're going to use.

**VMware** develops virtualization software that allows us to create and run virtual machines on our computer.

For desktop virtualization, VMware provides two closely related applications:

| Operating System | Application                |
| ---------------- | -------------------------- |
| macOS            | **VMware Fusion Pro**      |
| Windows / Linux  | **VMware Workstation Pro** |

Both are desktop **hypervisors**.
A hypervisor allows us to create virtual computers that use resources from our physical computer.

For example, instead of needing three physical computers for our Docker Swarm:

```text
Physical Computer
 Virtual Machine 1
 Virtual Machine 2
 Virtual Machine 3
```

Each virtual machine behaves like its own computer with its own operating system, network configuration and Docker installation.

This is exactly what we need later when we want to experiment with multiple Docker hosts.

---

# VMware and Broadcom

If you search for VMware today, you'll quickly encounter the name **Broadcom**.
That's because **Broadcom acquired VMware on November 22, 2023**.
As part of the acquisition, VMware became part of Broadcom and VMware's shares stopped trading independently on the New York Stock Exchange.

This also explains why VMware downloads and account management are now handled through **Broadcom's systems**.

> [!INFO] Broadcom Acquisition
> Broadcom's official announcement:
> https://investors.broadcom.com/news-releases/news-release-details/broadcom-completes-acquisition-vmware

---

# Is VMware Free?

Yes.

This is slightly confusing because the desktop applications are still called:

- **VMware Fusion Pro**
- **VMware Workstation Pro**

Historically, **Pro** indicated the paid versions of these products.

That is no longer how the current versions are licensed.

As of **November 11, 2024**, VMware Fusion and VMware Workstation became available at no charge for:
- Personal use
- Educational use
- Commercial use

The current versions don't require purchasing a license or entering a license key.

> [!TIP]
> Don't let the word **Pro** confuse you.
> **Fusion Pro** and **Workstation Pro** are currently free to use even today: 2026 18:th of September

VMware continues to provide updates and security patches, but users of the free products don't receive support through Broadcom's Global Support Team.

Official VMware Desktop Hypervisor FAQ:
https://www.vmware.com/docs/desktop-hypervisor-faqs

---

# Which Version Should I Use?

The choice mainly depends on your operating system.

### macOS
Use: **VMware Fusion Pro**
Fusion is VMware's desktop hypervisor for Mac.

### Windows or Linux
Use: **VMware Workstation Pro**
Workstation is VMware's desktop hypervisor for Windows and Linux.

You can read more about both products here:
https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion#product-overview

---

# Before We Download

VMware software is now distributed through the **Broadcom Support Portal**.
Before downloading anything, we'll create a Broadcom account.

First you have to register at broadcom:
https://profile.broadcom.com/web/registration

Once your account has been created, sign in to the Broadcom Support Portal.

> [!NOTE]
> The download process has several steps and isn't particularly obvious the first time.
>
> Follow the steps below rather than trying to find the correct download directly from the VMware product page.

---

# Download VMware

Once you've logged in to Broadcom, open:

**My Downloads**

![[broadcom-navigate-downloads-after-login.png|229]]

Look for:

**Free Software Downloads available HERE**

![[broadcom-navigate-downloads-after-login-my-downloads.png]]

This takes us to the software currently available for free.

---

## macOS

Find:

**VMware Fusion**

![[broadcom-navigate-downloads-after-login-my-downloads-fusion.png]]

Select the current version of **VMware Fusion**.

**VMware Fusion 25H2** (as of writing)

![[vmware-fusion-instruction-title-25h2.png]]

Before downloading, you may need to accept the **Terms and Conditions**.

![[agree-terms-of-condition-read-first.png]]

![[verify-cloud-icon-button.png]]

Complete the required information using your **real account details** and submit the form.

![[form-fill-in-bogus.png]]

You can then download VMware Fusion.

---

## Windows / Linux

If you haven't already downloaded it, you can also find them at: https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion

Look for:
**VMware Workstation Pro**

![[vmware-workstation-linux-windows.png|393]]

Choose the version matching your operating system:

- VMware Workstation Pro for **Windows**
- VMware Workstation Pro for **Linux**

Then follow the same download and verification process.

---

# macOS Permissions

When VMware Fusion starts for the first time, macOS may request additional permissions.

For example, VMware may request **Accessibility** access so that keyboard and mouse input works correctly inside virtual machines.

![[mac-accessibility-instruction.png|510]]

Select **OK**.

Then open:

**System Settings - Privacy & Security **

Enable **VMware Fusion**.

![[mac-permissions-guide-vmware.png]]

> [!SUCCESS]
> VMware Fusion is now installed and ready to create virtual machines.

---

# What Did We Learn?

We now know that:

- VMware provides software for creating and running virtual machines.
- **Fusion Pro** is used on macOS.
- **Workstation Pro** is used on Windows and Linux.
- Broadcom acquired VMware in 2023.
- Fusion Pro and Workstation Pro are currently free for personal, educational and commercial use.
- VMware downloads are handled through the Broadcom Support Portal.
- A Broadcom account is required for the download process.

---

# What's Next?

VMware gives us the **hypervisor**, but we still don't have any virtual machines.
Let's look at our options!

For KALI setup (pentesting) check out: [[VM - Vmware - Download Kali]]