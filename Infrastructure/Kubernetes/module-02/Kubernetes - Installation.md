---
icon: LiDownload
---
# Table of Contents

- [[#Overview|Overview]]
- [[#Install Kubernetes Tools|Install Kubernetes Tools]]
	- [[#macOS Installation|macOS Installation]]
	- [[#Windows Installation|Windows Installation]]
	- [[#Linux Installation|Linux Installation]]
- [[#Create Your First Kubernetes Cluster|Create Your First Kubernetes Cluster]]
- [[#Verify the Installation|Verify the Installation]]
- [[#Optional - Other Ways to Run Kubernetes|Other Ways to Run Kubernetes]]
- [[#Installation Complete|Installation Complete]]
- [[#Next Step|Next Step]]

# Overview

![[kubernetes-banner.jpg]]

## What Are We Installing?

Unlike Docker, Kubernetes isn't a single application that we simply install and start.

For our local learning environment, we'll use two tools:
- **kubectl** - the command-line tool we use to communicate with Kubernetes.
- **kind** - creates a local Kubernetes cluster using Docker containers.

This means **Docker must already be installed and running** before continuing.
- [Kubernetes - Install Tools](https://kubernetes.io/docs/tasks/tools/)
- [kubectl Installation](https://kubernetes.io/docs/tasks/tools/#kubectl)
- [kind - Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start/)

> [!info]- What is kind?
> **kind** stands for **Kubernetes IN Docker**.
>
> It creates a local Kubernetes cluster where the **Kubernetes Nodes run as Docker containers**.
>
> This makes it a lightweight and convenient way to run Kubernetes locally for **learning, testing and development**, without needing separate virtual machines or cloud infrastructure.
>
> In this course, we'll use **kind** to create the Kubernetes environment that we'll practice with.

> [!warning] Before You Begin
> Your **kubectl version should be within one minor version of the Kubernetes cluster** you're connecting to.
>
> For example, if your cluster is running **Kubernetes v1.37**, you can use:
> - kubectl v1.36
> - kubectl v1.37
> - kubectl v1.38
>
> In simple terms, **kubectl and Kubernetes shouldn't be too far apart in version**. Since `kubectl` communicates with the cluster's API Server, large version differences can introduce compatibility problems.
>
> For this course, using a recent version of `kubectl` with the cluster created by **kind** will keep the versions compatible.

---

# Install Kubernetes Tools

## Step 1 - Check Your System

The tools we'll use are available for:
- macOS
- Windows
- Linux

This document is based on the following source:
- [Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
- [Install kubectl on macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
- [Install kubectl on Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)

Choose the installation instructions for your operating system.

---

# macOS Installation

![[mac-logo.jpg|226]]

## Step 2 - Install kubectl

If you have **Homebrew** installed, open the Terminal and run:

```bash
brew install kubectl
```

> [!note]- Don't use Homebrew?
> Homebrew is **not required**.
>
> `kubectl` and `kind` can also be installed manually by downloading their binaries.
> - [Install kubectl on macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
> - [Install kind](https://kind.sigs.k8s.io/docs/user/quick-start/)
>
> We use Homebrew in this guide because it provides the simplest installation and update process on macOS.

Verify the installation:

```bash
kubectl version --client
```

You should receive information about the installed kubectl version.

---

## Step 3 - Install kind

Install kind using Homebrew:

```bash
brew install kind
```

Verify the installation:

```bash
kind version
```

If both commands return version information, our tools are installed.

---

# Windows Installation

![[Terminal - Windows os png.png|251]]

## Step 2 - Install kubectl

Open **PowerShell**.

If you're using Windows Package Manager, run:

```powershell
winget install -e --id Kubernetes.kubectl
```

Verify the installation:

```powershell
kubectl version --client
```

---

## Step 3 - Install kind

Install kind using Windows Package Manager:

```powershell
winget install Kubernetes.kind
```

Verify the installation:

```powershell
kind version
```

> **Note**
>
> Other installation methods are available for Windows, including Chocolatey and Scoop. See the official installation guides if you're not using `winget`.

---

# Linux Installation

![[Terminal - Linux os png.png|242]]

## Step 2 - Install kubectl

The exact installation method depends on your Linux distribution.

Kubernetes provides installation instructions for package managers such as `apt`, `dnf` and `zypper`, as well as direct binary installation.

Follow the official guide for your distribution:

[Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

Once installed, verify it with:

```bash
kubectl version --client
```

---

## Step 3 - Install kind

kind provides binaries for both **AMD64** and **ARM64** Linux systems.

Because the installation command depends on your architecture, follow the official installation instructions:

[kind - Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start/)

Once installed, verify it with:

```bash
kind version
```

---

# Create Your First Kubernetes Cluster

## Step 4 - Check Docker

Before creating our cluster, make sure Docker is running:

```bash
docker --version
```

You can also verify that the Docker Engine is available:

```bash
docker info
```

> **Important**
> kind uses Docker to create the Nodes in our local Kubernetes cluster.
> Docker therefore needs to be running before we create the cluster.

---

## Step 5 - Create the Cluster

Check your containers & images:

```bash
$ docker ps -a
$ docker images
```

Create cluster:

```bash
kind create cluster --name kubernetes-practice
```

kind will now create a local Kubernetes cluster.

Once it's finished, check your containers again:

```bash
docker ps
```

You should now see a container named: `kubernetes-practice-control-plane`

Notice the **PORTS** column. You should see something similar to: `127.0.0.1:56947->6443/tcp`
Copy your `127.0.0.1` address and port, add `https://`, and visit it in your browser:

> `https://127.0.0.1:56947`

Your port will likely be different.

You won't see a normal website. You're connecting directly to the **Kubernetes API Server**. 

![[api.svg|150]]

Port `6443` is the API Server inside our Kubernetes Node, while kind has exposed it through a local port on our computer.

Remember when we called the API Server the **front door to Kubernetes**? We're now looking at that front door ourselves.

![[kubernetes-components-diagram-overview.png]]

Now try this:

```bash
docker images -a
```

If you get `<untagged>` image, do not fret, this is expected.

> [!note] Why does the kind image appear as `<untagged>`?
>
> kind pins its Node image using an **image digest**, which identifies the exact image it expects. Because the image may not have the usual local `repository:tag` association, Docker can display it as `<untagged>`.
>
> The image is still there and being used. You can confirm this with `docker ps`, which will show the familiar image name, such as `kindest/node:v1.37.0`.


---

# Verify the Installation

## Step 6 - Check the Cluster

Now let's see whether `kubectl` can communicate with our cluster:

```bash
kubectl cluster-info
```

If the cluster is running correctly, Kubernetes should return information about the cluster and its Control Plane.

---

## Step 7 - Check the Nodes

Remember that Kubernetes runs workloads on **Nodes**.

Run:

```bash
kubectl get nodes
```

You should see something similar to:

```text
NAME                                STATUS   ROLES
kubernetes-practice-control-plane   Ready    control-plane
```

> **Success**
> If the Node reports `Ready`, our Kubernetes cluster is running.

---

# Optional - Other Ways to Run Kubernetes

For this course, we'll use **kind** because it provides a simple and lightweight way to create Kubernetes clusters using Docker. We won't be using anything new here.

**However, kind isn't the only option.** 
Depending on what you want to experiment with, you may eventually encounter other tools.

## minikube

Like kind, **minikube** lets you run Kubernetes locally on Windows, macOS and Linux.

minikube provides more options for configuring your local environment and can create both **single-node and multi-node clusters**.

This makes it useful if you want to experiment with Kubernetes locally beyond what we need for this course.
- [minikube - Official Website](https://minikube.sigs.k8s.io/)
- [minikube - Get Started](https://minikube.sigs.k8s.io/docs/start/)

> [!tip]
> **kind** is perfectly suitable for following this course.
> Consider **minikube** if you later want a more configurable local Kubernetes environment.

## kubeadm

**kubeadm** takes us a step further.

Rather than providing a convenient local development environment, kubeadm helps you **create and configure a Kubernetes cluster yourself**.

It handles much of the initial setup required to get a minimum viable Kubernetes cluster running, while still leaving you responsible for the machines and much of the surrounding cluster configuration.

This makes kubeadm useful when you want to learn more about **how Kubernetes clusters are actually built and administered**.

- [kubeadm - Official Documentation](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)
- [Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Creating a Cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

---

# Installation Complete

At this point you should have:
- Docker installed and running
- kubectl installed
- kind installed
- A local Kubernetes cluster
- Successfully connected to the cluster using kubectl
- A Kubernetes Node reporting `Ready`

Your local Kubernetes environment is ready.

---

# Next Step

Now that we have a running Kubernetes cluster, we can start interacting with it using **kubectl**.

In the next section, we'll explore some basic kubectl commands.

Continue on: [[Kubernetes - Hands-On kubectl Practice 02]]
