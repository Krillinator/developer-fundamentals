---
icon: LiDownload
---
# Docker Desktop

![[docker-desktop-banner.webp]]

> [!info] What is Docker Desktop?
> Docker Desktop provides the tools needed to build and run Docker containers locally.
>
> It includes Docker Engine, Docker CLI, Docker Compose and other Docker tools.

- [Docker Desktop - Official Website](https://www.docker.com/products/docker-desktop/)
- [Docker Desktop - Official Documentation](https://docs.docker.com/desktop/)
- [Docker Installation Guide](https://docs.docker.com/get-started/get-docker/)

---

# Install Docker Desktop

## Step 1 - Check Your System

Docker Desktop is available for:

- macOS 
- Windows
- Linux

Choose the installation instructions for your operating system.

> [!note]
> Windows installation is covered briefly below.

---

# macOS Installation

## Step 2 - Check Your Mac Processor

Before downloading Docker Desktop, check whether your Mac uses an **Apple chip** or an **Intel processor**.

1. Click the **Apple menu ()**
2. Select **About This Mac**
3. Look for **Chip** or **Processor**

Examples:

```text
Chip: Apple M3
```

or:

```text
Processor: Intel Core i7
```

> [!tip]
> M1, M2, M3, M4 etc. are **Apple silicon**.
>
> Older Macs may use an **Intel processor**.

---

## Step 3 - Download Docker Desktop

Open the official Docker installation page:

[Install Docker Desktop on Mac](https://docs.docker.com/desktop/setup/install/mac-install/)

Download the correct version for your Mac:

- **Mac with Apple silicon**
- **Mac with Intel chip**

The downloaded file should be:

```text
Docker.dmg
```

---

## Step 4 - Install Docker Desktop

1. Open `Docker.dmg`
2. Drag **Docker** into the **Applications** folder
3. Open **Applications**
4. Start **Docker**

macOS may ask for permission during the initial setup.

> [!note]
> Docker Desktop may request authorization for certain system-level configurations during installation.

---

## Step 5 - Start Docker Desktop

Open:

```text
Applications → Docker
```

Wait for Docker Desktop to finish starting.

You should see Docker running in the macOS menu bar.

> [!warning]
> Docker commands may fail while Docker Desktop is still starting.
>
> Wait until Docker Desktop reports that Docker is running before continuing.

---

# Windows Installation

## Step 2 - Download Docker Desktop

Open the official installation page:

[Install Docker Desktop on Windows](https://docs.docker.com/desktop/setup/install/windows-install/)

Download:

```text
Docker Desktop Installer.exe
```

---

## Step 3 - Run the Installer

1. Open `Docker Desktop Installer.exe`
2. Follow the installation wizard
3. Use **WSL 2** when available
4. Complete the installation
5. Start Docker Desktop

> [!info] WSL 2
> Docker Desktop can use **Windows Subsystem for Linux 2 (WSL 2)** to run Linux containers on Windows.
>
> For most Windows users, this is the recommended backend.

---

# Verify the Installation

## Step 6 - Check Docker

Open a terminal.

On macOS:

```text
Use the Terminal
```

On Windows:

```text
Use Bash or PowerShell
```

Run:

```bash
docker --version
```

You should receive something similar to:

```text
Docker version XX.X.X, build XXXXXXX
```

> [!success]
> If Docker returns a version number, the Docker CLI is installed correctly.

---

## Step 7 - Run Your First Container

Run:

```bash
docker run hello-world
```

Docker will:

1. Look for the `hello-world` image locally
2. Download it if it does not exist
3. Create a container
4. Run the container
5. Print a confirmation message

You should eventually see:

```text
Hello from Docker!
```

> [!success] Docker is Ready
> If you see **Hello from Docker!**, Docker is installed and able to run containers.

---

# Optional - Sign In

You can use Docker Desktop without immediately signing in for basic local work.

A Docker account becomes useful when working with services such as **Docker Hub**.

[Docker Hub](https://hub.docker.com/)

> [!note]
> Signing in allows Docker Desktop to access your Docker Hub repositories and provides higher image pull limits than anonymous usage.

---

# Installation Complete

At this point you should have:

- Docker Desktop installed
- Docker Desktop running
- Access to the `docker` command
- Successfully run `hello-world`

> [!success] Ready
> Your local Docker environment is ready.

---

# Next Step

Now that Docker is installed, continue with:

[[Docker - Start Here]]

This is where the Docker theory and workflow begin.