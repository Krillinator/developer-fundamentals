---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

We've created our first local Kubernetes cluster using **kind** and started using **kubectl** to interact with it. Let's explore the environment itself.

Here are some commands that may be useful during the lab:

```bash
kubectl
kubectl --help
kubectl get --help
kubectl api-resources
kubectl cluster-info
kubectl get nodes
kubectl get namespaces
kubectl get pods
kubectl get pods -A
kind get clusters
kind --help
```

You don't need to use every command, and you'll need to discover some commands yourself.

> **The goal is to explore your cluster, discover Kubernetes resources using `kubectl`, and then safely remove the cluster when you're finished.**

---

# 1. Explore kubectl

Start by finding out what `kubectl` can do.

Use its built-in help:

```bash
kubectl --help
```

Look through the available commands and find the command used to **display Kubernetes resources**.

> Hint: look for 'get'

Once you've found it, investigate that command further using its own `--help` option.

Don't worry about understanding every option. The goal is to get comfortable discovering commands without being given the exact syntax.

---

# 2. What Can We Get?

So.. what else can we put after `get`?
You can find this out by inserting an incomplete command into the terminal:

``` shell
kubectl get
```

*"You must specify the type of resource to get. Use "kubectl api-resources" for a complete list of supported resources"*

Follow the instructions

``` shell
kubectl api-resources
```

Explore the output and pay attention to columns such as:

```text
NAME
SHORTNAMES
APIVERSION
NAMESPACED
KIND
```

Try using some of the resources you recognize with:

```text
kubectl get <resource>
```

See if you can find resources we've already discussed, such as:
- Nodes
- Pods
- Namespaces
- Services

Also look at the **SHORTNAMES** column. Some Kubernetes resources have shorter names that can be used with `kubectl get`.

> [!info]- More Namespaces?
> Earlier, we focused on `default` and `kube-system`. 
>
> ![[kubernetes-objects-default-namespace-diagram.png]]
>
> A real cluster contains some additional namespaces used by Kubernetes itself, such as `kube-public` and `kube-node-lease`.
>
> ![[kubernetes-kind-namespaces-default-terminal.png|250]]
>
> Our kind cluster also contains `local-path-storage`, which is used by the local storage provisioner installed for the cluster.
>
> We don't need to understand these yet. For now, the important lesson is that **namespaces aren't only for our applications; Kubernetes and cluster add-ons use them to organize their own resources too.**

> [!info]- Why is the Cluster IP useful to know?
> Imagine our frontend needs to communicate with a backend running across several Pods. Those Pods can be replaced and their IP addresses can change, so we don't want the frontend connecting directly to a specific Pod.
>
> Instead, it connects to a **Service** using its stable Cluster IP. The Service then provides access to the appropriate backend Pods.
>
> **Frontend - Service (`10.96.20.50`) - Backend Pods**
>
> The service gives us a stable destination even when the Pods behind it change.

---

# 3. Find the Kubernetes Endpoint

Somewhere in the available resources you'll find a resource called:

```text
endpoints
```

Find its short name and use it with `kubectl get`.

You should discover an endpoint belonging to Kubernetes itself, pointing toward port:

```text
6443
```

Port 6643 is the default secure port for the Kubernetes API server which acts as the front door to Kubernetes.

However, there's also something else interesting in the output.
**Read the warning carefully.**

Kubernetes tells you that the `Endpoints` resource is deprecated and recommends another resource instead.

Your next task is to find that replacement.

Use what you've learned about:

```bash
kubectl api-resources
kubectl get
kubectl --help
```

Find the newer resource, check whether it has a short name, and use it to inspect the cluster.

---

# 4. Remove the Cluster

Once you've finished exploring, remove the cluster.

But **don't delete the Docker container manually**.

Remember how the cluster was created:

```bash
kind create cluster --name kubernetes-practice
```

Use `kind --help` to discover how clusters are removed.

Then remove:

```text
kubernetes-practice
```

without being given the finished command.

Afterward, verify the result from both perspectives.

Check kind:

```bash
kind get clusters
```

And check Docker:

```bash
docker ps -a
```

The Kubernetes cluster and its Node container should now be gone.

---

# Solution

## Exploring kubectl

Display kubectl's available commands:

```bash
kubectl --help
```

Get help specifically for `get`:

```bash
kubectl get --help
```

Discover the resources supported by the cluster:

```bash
kubectl api-resources
```

Inspect the Node:

```bash
kubectl get nodes
```

Inspect namespaces:

```bash
kubectl get namespaces
```

Inspect Pods across all namespaces:

```bash
kubectl get pods -A
```

---

## Finding Endpoints

`kubectl api-resources` shows that `Endpoints` has the short name:

```text
ep
```

So we can run:

```bash
kubectl get ep
```

On Kubernetes v1.33 and later, this produces a deprecation warning telling us to use **EndpointSlice** instead.

Find it in:

```bash
kubectl api-resources
```

EndpointSlice does NOT have a short name.
You can use the full resource name:

```bash
kubectl get endpointslices
```

---

## Removing the Cluster

Find the available kind commands:

```bash
kind --help
```

Then remove the cluster:

```bash
kind delete cluster --name kubernetes-practice
```

Verify that kind no longer has the cluster:

```bash
kind get clusters
```

Then check Docker:

```bash
docker ps -a
```

The `kubernetes-practice-control-plane` container should also be gone.

---

# What Did We Practice?

This lab was about learning to **explore Kubernetes independently** using `kubectl`, built-in help and available resources, then safely cleaning up the cluster when finished.

That distinction will become increasingly important:

> **Docker can see the container that kind uses as a Node, but Kubernetes and kind give us the tools for managing the Kubernetes environment built on top of it.**

Continue on: [[Kubernetes - Exploring Our Kubernetes Cluster]]