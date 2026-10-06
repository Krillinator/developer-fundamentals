---
icon: LiCog
---
# Installing Kali Linux

Our virtual machine has now been created and the Kali installer ISO is attached.

The next step is to actually **install Kali Linux onto the virtual machine's disk**.

Remember that we're working inside a virtual machine. When the installer talks about disks, partitions, hostnames and users, it's configuring the **virtual computer**, not our physical computer.

> [!IMPORTANT]
> The disk shown inside the Kali installer is the **virtual disk created for the VM**.
>
> Formatting or partitioning this disk does not mean formatting the disk containing Windows or macOS on the host computer.

---

# Start the Installer

Start the Kali virtual machine.

The VM should boot from the Kali installer ISO and display the Kali boot menu.

Select:

**Install**

![[gmu-grub-select-install-menu.png]]

Kali also provides options such as **Graphical install**. Both ultimately install Kali, but the regular installer is perfectly suitable for what we're doing.

---

# Select the Language

First, choose the language that should be used during the installation.

Select: **English**

![[gmu-grub-select-english.png]]

The language selected here also helps determine some of the defaults used later during installation.

---

# Select Your Location

Next, Kali asks for your location.
If your country isn't shown immediately, select: **Other - Europe**

![[gmu-grub-select-other-europe.png]]

Then select your country of origin.

![[gmu-grub-select-country-origin.png|278]]

This information helps Kali configure regional settings such as locale and time.

---

# Configure the Locale

The installer may ask which locale should be used.
For an English installation, we can use:
**en_US.UTF-8**

![[gmu-grub-select-en-us-utf8.png|439]]

UTF-8 is the character encoding used by the system and supports a very large range of characters and languages.

> [!NOTE]
> Your language, physical location and locale don't necessarily have to be identical.
>
> For example, you can live in Sweden while still using an English system with an `en_US.UTF-8` locale.

---

# Configure the Keyboard

Select the country or layout that matches your physical keyboard.

![[gmu-grub-select-keymap-country-origin.png|288]]

This determines how Kali interprets your keyboard input.
For example, Swedish and US keyboards have different layouts for several symbols.

---

# Configure the Clock

The installer will configure the system clock based on the location information we've provided.

![[gmu-grub-select-configure-clock.png|472]]

Select the appropriate timezone when prompted.
This determines how Kali displays local time inside the virtual machine.

---

# Configure the Network

Kali will now begin configuring networking and ask us to identify the machine.
Every computer on a network can have a **hostname**.

![[gmu-grub-select-hostname-for-system.png]]

The hostname is the name used to identify this particular system.
For our virtual machine, we can use:

```text
kali-vm
```

![[gmu-grub-select-hostname-kali-vm.png|168]]

> [!TIP]
> Descriptive hostnames become particularly useful when working with multiple VMs.
>
> Names such as `kali-vm`, `docker-manager` or `docker-worker-01` immediately communicate which machine we're working with.

---

# Domain Name

The installer may also ask for a **domain name**.

![[gmu-grub-select-domain-name.png]]

A domain name is useful when the machine belongs to a larger managed network or DNS domain.

For a simple local Kali VM, we don't need one.
Leave the field empty and continue.

> [!NOTE]
> **Hostname** identifies this machine.
>
> **Domain name** identifies the larger domain the machine belongs to.
>
> For our standalone local VM, a hostname is enough.

---

# Create the User

The installer will now configure users and passwords.

![[gmu-grub-select-set-up-users-and-passwords.png]]

Kali asks for the **full name** of the person using the system.

![[gmu-grub-select-full-name-for-new-user-non-administrative.png]]

This is primarily descriptive information associated with the account.
You'll also be asked to create a **username** and **password** for the account.
Choose credentials that you can remember.

> [!IMPORTANT]
> Kali uses a regular non-root user by default.
>
> Administrative commands can be performed using `sudo` when elevated privileges are required.

---

# Partition the Virtual Disk

Kali now needs somewhere to install the operating system.
This is where **disk partitioning** comes in.

Select: **Guided - use entire disk**

![[gmu-grub-select-disc-partioning-guided-use-entire-disk.png]]

We're telling Kali that it can use the entire **virtual disk assigned to this VM**.

> [!TIP]
> Guided partitioning is a good choice for a learning VM because Kali handles the partition layout for us.

---

# Select the Virtual Disk

The installer will show the available disk.
When using VMware, you may see something similar to:

```text
/dev/nvme0n1
```

![[gmu-grub-select-select-disk-dev-nvme0n1-vmware.png]]

Select the virtual disk created for the Kali VM.

> [!NOTE]
> Linux represents storage devices using names such as `/dev/sda` or `/dev/nvme0n1`.
>
> The exact name can differ depending on the virtual hardware presented by VMware.

---

# Choose the Partition Layout

Next, select: **All files in one partition**

![[gmu-grub-select-all-files-in-one-partition-recommended.png]]

This keeps the virtual disk layout simple and is a good choice for our learning environment.
More advanced systems may separate directories such as `/home`, `/var` or `/tmp` into their own partitions, but we don't need that complexity here.

---

# Finish Partitioning

The installer will display the proposed partition layout.
Select: **Finish partitioning and write changes to disk**

![[gmu-grub-select-finish-partioning-and-write-changes-to-disk.png]]

Kali will ask us to confirm before making the changes.
Select: **Yes**

![[gmu-grub-select-write-changes-to-disk-yes.png]]

The installer can now create the partitions and begin installing Kali onto the virtual disk.

> [!IMPORTANT]
> This is the point where the selected virtual disk is actually modified.
>
> Always make sure the correct disk is selected before confirming disk changes.

---

# Software Selection

During installation, Kali lets us choose which software and desktop environments should be installed.

![[gmu-grub-select-software-selection-deselect-gnome-kde.png]]

For a normal learning environment, we don't need several desktop environments installed simultaneously.

If **GNOME** and **KDE Plasma** are offered as additional desktop choices, they can be deselected unless you specifically want to use them.

Keeping the installation focused reduces unnecessary software and disk usage.

> [!TIP]
> More software isn't automatically better.
>
> Start with what you actually need. Additional packages can always be installed later.

---

# Install the GRUB Bootloader

Near the end of the installation, Kali installs **GRUB**.

![[gmu-grub-select-install-grub-boot-loader.png]]

GRUB is the program responsible for starting the operating system when the virtual machine boots.

Without a bootloader, the operating system could exist on the virtual disk without the normal mechanism used to locate and start it.

> [!NOTE]
> **GRUB is not Kali itself.**
> GRUB is the bootloader that helps start Kali.

---

# Finish the Installation

Once the remaining files and bootloader have been installed, Kali will tell us that the installation is complete.

![[gmu-grub-select-finish-installation.png|430]]

Select:

**Continue**

![[gmu-grub-select-finish-installation-continue.png]]

The virtual machine will restart.

At this point, it should boot from the installed Kali system on its **virtual disk** rather than starting the installer again.

---

# First Boot

After restarting, Kali should eventually display its login screen.

![[gmu-grub-select-kali-bootup-login.png]]

Sign in using the **username and password you created during installation**.


> [!SUCCESS]
> Kali Linux is now installed inside the virtual machine.

---

# What Did We Learn?

We started with an empty virtual machine and a Kali installer ISO.

During installation, we configured the system's language, locale, keyboard, clock and network identity. We created a user, partitioned the VM's virtual disk, selected the software to install and installed GRUB so the operating system can boot from that disk.

The important distinction is that all of this happened **inside the virtual environment**:

> **VMware created the virtual computer.**
>
> **The Kali installer installed the operating system onto that computer.**

We now have a functioning Linux environment that we can configure, experiment with and even break without turning our physical computer into the test environment.

---

# What's Next?

Kali is installed, but our virtual machine still has several settings worth exploring.

From here, we can branch into different topics depending on what we want to configure:

### Network Settings

Learn how the VM communicates with the host, internet and other virtual machines using networking modes such as **NAT** and **bridged networking**.
[[VMware Configuration - Network Settings]]

### Disk Encryption

Explore encrypted storage and when encryption makes sense for a virtual machine.
[[VMware Configuration - Encryption]]

### Disk Size

Understand how virtual disks work, how much storage Kali has and how we can expand the disk if more space is needed.
[[VMware Configuration - Disk Size]]

### Snapshots

Capture the state of the VM before making risky changes so we can return to an earlier working state.
[[VMware Configuration - Snapshot]]

### Performance

Adjust CPU and memory allocation and understand how resources assigned to the VM affect the physical host.
[[VMware Configuration - Performance]]

### Password Reset

Learn how account recovery works if we forget the Kali user's password.
[[KALI - Password Reset]]

> [!TIP]
> **Snapshots** are particularly useful for learning environments.
>
> Before experimenting with something that might break Kali, take a snapshot. You can then experiment freely and restore the previous state if necessary.