---
icon: LiDownload
---
# Downloading Kali Linux for VMware

Now that VMware is installed, we need an operating system for our virtual machine.
For this course, we'll use **Kali Linux**.

![[Pasted image 20260918133726.png|333]]

Kali provides a pre-built VMware virtual machine, which is the easiest option when your computer uses the **x86_64 / amd64 architecture**.

Apple Silicon Macs are different because they use the **ARM64 architecture**, so we'll need to use a different installation method.

---

# Intel / AMD Computers

If you're using a typical Windows PC or an older Intel-based Mac, Kali provides a **pre-built VMware virtual machine**.

Visit: https://www.kali.org/get-kali/#kali-virtual-machines

Look for: **VMware**

![[vmware-download-kali-browser.png|186]]

Download and extract the VMware image.

The advantage of this option is that Kali has already created and configured the virtual machine for us.

---

# Apple Silicon Macs

Modern Macs use Apple's own processors instead of Intel processors.
This includes:
- M1
- M2
- M3
- M4
- Later Apple Silicon processors

These processors use the **ARM64 architecture**.
This creates an important difference when downloading Kali.

> [!WARNING] Apple Silicon
> Kali's pre-built **VMware** download is currently built for **amd64**.
>
> Do **not** use that image on an Apple Silicon Mac.
>
> Instead, we'll download Kali's **ARM64 Installer Image** and create the VMware virtual machine ourselves.


Visit Kali's Installer Images: https://www.kali.org/get-kali/#kali-installer-images

At the top of the downloads, select: **Apple Silicon (ARM64)**
Then find:

**Installer**

![[vmware-kali-arm64-download.png]]

Download the regular **Installer** image.
This gives us a file similar to:

```text
kali-linux-20XX.X-installer-arm64.iso
```

> [!TIP]
> Use the regular **Installer** unless you specifically have a reason to use another image.
>
> It contains what we need for a normal offline Kali installation and allows us to customize the installation.

Kali's official Apple Silicon VMware documentation:
https://www.kali.org/docs/virtualization/install-vmware-silicon-host/

---

# Create the Virtual Machine

Open **VMware Fusion**.

![[vmware-create-new-vm.png|444]]

Create a new virtual machine and select: **Install from disc or image**

Then select the ARM64 Kali `.iso` we just downloaded.

![[vm-install-from-disc-downloaded-arm64-kali.png]]

The ISO acts as our **installation media**.

Unlike the pre-built VMware image, Kali hasn't been installed yet. We're creating the virtual machine and installing Kali ourselves.

---

# Choose the Operating System

VMware may not automatically identify the Kali installer.
That's okay.

Kali Linux is based on **Debian**, so we can configure the VM as a Debian system.
Select: **Linux**

Then select the appropriate ARM64 Debian option.

For example:
**Debian 12.x 64-bit Arm**

![[kali-vm-install-choose-operating-system.png]]

> [!NOTE] Why Debian?
> Kali Linux is based on Debian.
>
> Selecting Debian here helps VMware choose sensible default virtual hardware for the VM. It does **not** install Debian instead of Kali.
>
> Kali will still be installed from the ISO we selected.

Continue to the next step.

---

# Review the Virtual Machine

VMware will now display a summary of the virtual machine it's about to create.

![[kali-vm-install-finish.png]]

These resources come from your physical Mac.

For example, assigning the VM **2 GB of memory** means that memory will be available to the virtual machine while it's running.

The same principle applies to CPU cores and storage.

> [!NOTE]
> These settings can be changed later.
>
> We don't need to perfectly optimize the VM before we've even installed Kali.

Select: **Finish**

---

# Start the Virtual Machine

Start the newly created Kali virtual machine.

It should boot from the Kali installer ISO.

![[gmu-grub-select-install-menu.png|385]]

From here, we're no longer configuring VMware itself.
We've entered the **Kali Linux installer**.

Go to the next section to learn more about the configuration.

---

# What Did We Learn?

There are two important VMware installation paths depending on the architecture of our computer.

For an **Intel/AMD computer**, Kali provides a pre-built VMware virtual machine that can be downloaded, extracted and opened directly.

For an **Apple Silicon Mac**, we instead download Kali's **ARM64 Installer ISO**, create an ARM64 virtual machine in VMware Fusion and install Kali ourselves.

The important distinction is:

> **Pre-built VMware image** - Kali is already installed.
>
> **Installer ISO** - We create the VM and install Kali ourselves.

---

# What's Next?

Our Apple Silicon virtual machine is now created and ready to boot into the Kali installer.

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

Once that's complete, we'll have a fully functioning Kali Linux virtual machine running inside VMware.

