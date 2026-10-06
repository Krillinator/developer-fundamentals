---
icon: LiComponent
---
# Table of Contents

1. [Kind](#kind)
2. [Our Default kind Cluster](#our-default-kind-cluster)
3. [Where is the Control Plane?](#so-where-is-the-control-plane)
4. [Creating a Multi-Node kind Cluster](#creating-a-multi-node-kind-cluster)
   - [Preparing Our Project](#preparing-our-project)
5. [Describing Our Cluster](#describing-our-cluster)
6. [Creating the Multi-Node Cluster](#create-the-multi-node-cluster)
7. [What's Running on Our Nodes?](#whats-running-on-our-nodes)
8. [Visiting the Kubernetes API](#visit-the-kubernetes-api)
   - [The Control Plane Address](#the-kubernetes-control-plane-address)
   - [kubectl proxy](#kubectl-proxy)
   - [Exploring the API](#explore-the-api)
   - [CoreDNS](#what-about-the-coredns-address)
9. [Investigating a Pod](#investigating-a-pod)
   - [Following the CoreDNS Workload](#what-is-the-pod-actually-running)
10. [What About Our Applications?](#what-about-our-applications)

# Overview

![[undraw_deconstructed_izoh.svg|464]]


**It is time to work practically**
Now that we've created a real cluster, we can finally go back to our question from before:

> **WHERE are these components actually running?**

This time we're going to connect our architecture diagrams to the actual cluster in front of us and find out **where Kubernetes has actually placed everything**. 

And since we're now using `kind`, things become slightly different from what we're used to..

---

**Why does this even matter?**
Because Kubernetes has to work in different environments, and one of Kubernetes' strengths is its flexibility. A Kubernetes cluster might run across physical servers, Virtual Machines in the cloud, or even containers on our own computer.

These environments don't all look the same.

Remember our original architecture:

![[kubernetes-components-diagram-overview.png]]

This diagram tells us **what the different parts of Kubernetes do**:
- The **Control Plane** manages the cluster.
- **Nodes** provide environments where workloads can run.
- Components such as the **API Server, Scheduler and kubelet** each have their own responsibilities.

**However..** the diagram doesn't tell us exactly **where those components have to run**.

That's important because Kubernetes can adapt to different infrastructure. The responsibilities stay the same, while **where they actually run can vary**.

*Let's get started*

--- 

# Kind

Now we're working with a perfect example: **kind**.
`kind` stands for:

> **Kubernetes IN Docker**

Instead of separate physical machines or Virtual Machines, kind uses **containers as Kubernetes Nodes**.

We'll start with the simplest case: **the cluster we've already created.**


---

# Our Default kind Cluster

When we ran:

```bash
kind create cluster --name kubernetes-practice
```

Let's see what kind created:

```bash
kubectl get nodes
```

![[kubernetes-kubectl-get-nodes-results-terminal.png]]


---

## Our kind Cluster

When we created our cluster we didn't tell kind how many Nodes to create.
By default, kind creates **one Node with the `control-plane` role**.

Remember what makes kind different from a traditional cluster: this Node is provided by a **container**.

Run:

```bash
docker ps
```

and you'll find the same environment from Docker's perspective:

![[kubernetes-kubectl-created-docker-ps-results-terminal.png|700]]

The container provides the **Node environment**, while Kubernetes sees it as a **Node participating in our cluster**.

**Isn't that interesting?**
From Docker's perspective, it's a **container**. 
From Kubernetes' perspective, it's a **Node**.
We're looking at the same environment from two different architectural layers.

*This is the key!*

---

# So where is the Control Plane?

Our kind cluster currently has **one Node**:  
  
```text  
kubernetes-practice  
```  
  
But earlier, our architecture looked like this:  
  
```text  
Kubernetes Cluster  
	Control Plane  
	Worker Nodes  
```  
  
So where did the **Control Plane** go?

**Well... we've actually already found it.**
Look at the name of our Node: `kubernetes-practice-control-plane`

![[kubernetes-kubectl-get-nodes-results-terminal.png]]

And look at the role Kubernetes gives it: `control-plane`

Our default kind cluster has **one Node, and that Node is the control-plane Node**.

This means the Control Plane components we've already learned about, such as the **API Server, etcd, Scheduler and Controller Manager**, are running on this Node.

![[kubernetes-default-kind-cluster-hierarchy-diagram.png]]

**And there it is!**  
In the image above, we can finally see where the Control Plane actually lives: **its components are running on our Kubernetes Node.**

Let's connect this back to our original architecture diagram.

![[kubernetes-components-diagram-overview.png]]

Previously, we showed the **Control Plane** separately from the Nodes to make their different roles easier to understand. The Control Plane manages the cluster, while Nodes provide the environments where Kubernetes components and workloads can run.

Our default kind cluster we just created together in the previous module makes this more concrete:

> **We currently have one Kubernetes Node, provided by one Docker container. That Node has the `control-plane` role and hosts the components that make up our Control Plane.**

Now we can see how kind has actually implemented it: the Control Plane components are running on our single Node:

![[kubernetes-default-kind-cluster-hierarchy-diagram.png]]



---

# Creating a Multi-Node kind Cluster

We can actually build this ourselves.
Why? Because we want to move away from a single Node

![[kubernetes-default-kind-cluster-hierarchy-diagram.png]]

to a multi-node kind cluster. 

![[kubernetes-kind-cluster-hierarchy-with-worker-best-practice-diagram.png]]

> [!NOTE] Workloads on the Control Plane?
> A control-plane Node **can run workloads**, which is useful in small or local clusters.
>
> In larger clusters, application workloads are typically kept on **worker Nodes**, leaving the control-plane Nodes focused on managing the cluster.


**Let us move on by first deleting our existing clusters.**
First, list your clusters:

```bash
kind get clusters
```

Find and remove all clusters:

```bash
kind delete cluster --name <name-of-cluster>
```


**Here's an interesting experiment..**  
Before we continue, try:

```bash
kubectl get nodes
```

You'll probably get a rather intimidating error ending with something similar to:

```text
The connection to the server localhost:8080 was refused
```


**Why?**  
Remember, `kubectl` is only a **client**.  
It needs a Kubernetes **API Server** to communicate with.

We just deleted our cluster, including the control-plane Node hosting its API Server. kind also removed its cluster configuration from our kubeconfig, leaving `kubectl` without that cluster to connect to.

`kubectl` is still installed and working. There's simply no Kubernetes cluster for it to communicate with right now.

> [!TIP] Think of it like this
> Imagine `kubectl` as a **remote control** and the Kubernetes cluster as the **TV**.
>
> You can still have the remote after removing the TV, but there's nothing for it to communicate with.
>
> That's essentially what happened here: `kubectl` still works, but the **API Server it needs to communicate with is gone**.

---

## Preparing Our Project

Before configuring the cluster, let's give our project a proper home.

Create a new project directory:

```bash
mkdir kubernetes-practice-02
cd kubernetes-practice-02
```

We'll eventually run applications across this cluster, so let's prepare two very small applications as well:

```bash
mkdir app-one app-two
touch kind-config.yaml
touch app-one/app.py
touch app-two/app.py
```

![[kubernetes-practice-02-folder-structure.png|181]]

`kind-config.yaml` describes the **local kind cluster we want to create**.
The application folders contain the **workloads we'll eventually run using Kubernetes**.

For now, add a simple Flask application to each folder (.py files).

### app-one/app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from App One!"

app.run(host="0.0.0.0", port=5000)
```

### app-two/app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from App Two!"

app.run(host="0.0.0.0", port=5000)
```

> [!IMPORTANT]
> These applications do **not** belong to a particular Node.
>
> We're simply preparing two workloads. Later, Kubernetes can decide which available Node should run their Pods.

Let's move on to the YAML file.

---

# Describing Our Cluster

Until now, we've let kind decide what our cluster should look like.

When we ran:

```bash
kind create cluster --name kubernetes-practice
```

kind used its **default configuration** and created a single control-plane Node.

This time, we want something different:
* x1 worker plane
* x1 worker nodes

Instead of asking kind to use its defaults, we'll describe this structure ourselves using 
`kind-config.yaml`.

Open the file: kind-config.yaml

Add:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

> [!TIP] Don't have YAML tools installed?
> YAML is sensitive to **spacing and indentation**, and tabs cannot be used for indentation.
> If your editor doesn't provide YAML formatting or validation, you can paste your YAML into:
> > https://onlineyamltools.com/edit-yaml
>
> It can help you **format and check your YAML** before using it.

Notice something important: `kind-config.yaml` doesn't mention `app-one` or `app-two` at all.
	`kind-config.yaml` describes the **kind infrastructure we want to create**.
	`app.py` files are simply **application source code**.

At this point, Kubernetes doesn't know anything about those applications yet.
We'll deal with that separately.

---

# Create the Multi-Node Cluster

Make sure you're standing in the root folder which in our demo is `kubernetes-practice-02` and where our current two folders + `yaml`file currently lives. 

Now give our configuration file to kind:

```bash
kind create cluster \
  --name kubernetes-practice-02 \
  --config ./kind-config.yaml
```

The `--config` option tells kind which configuration file to use.

This time, kind won't create its default single-Node cluster.
instead, It reads our configuration and creates the structure **we described**.

Once it finishes, inspect the result:

```bash
kubectl get nodes
```

> [!NOTE] Why do the Worker Nodes show `<none>`?
> Don't worry, they're still **Worker Nodes**.
>
> The ROLES column is based on special Kubernetes **Node labels**. 
> kind labels the control-plane Node as `control-plane`, but doesn't add a `worker` role label to its Worker Nodes.

You should now find **three Nodes**:

![[kubernetes-kubectl-get-nodes-yaml-nodes.png]]

And because we're using **kind**, each of these Nodes is actually provided by a Docker container.

Check:

```bash
docker ps
```

You should find three corresponding containers.

![[kubernetes-docker-ps-terminal-results.png]]

All three Nodes still belong to **one Kubernetes cluster**.

That's the important part: we've added more Nodes, but we haven't created more Kubernetes clusters.


---

# What's Running on Our Nodes?

Good job so far, we're now very close to finishing up this part, let's round up by asking ourselves: What is actually running on our nodes?

We can start by looking at all Pods in the cluster:

```bash
kubectl get pods -A
```

`-A` means **all namespaces**.

![[kubernetes-kubectl-get-pods-A-results-terminal.png]]

You'll see several Kubernetes system Pods, some of which we recognize, such as:
* etcd-kubernetes
* kube-apiserver-kubernetes
* kube-controller-manager
* kube-proxy
* kube-scheduler

> [!NOTE]- What are all these other Pods?
> You may also notice several components we haven't discussed yet:
>
> - `CoreDNS` provides **DNS inside the cluster**, helping workloads find services by name.
> - `kindnet` provides **networking between Nodes and Pods** in our kind cluster.
> - `kube-proxy` helps manage **network traffic to Services** on each Node.
> - `local-path-provisioner` provides **local storage** that Kubernetes workloads can request.

but we still don't know **which Node each Pod is running on**.
Add the `-o wide` option:

```bash
kubectl get pods -A -o wide
```

> [!NOTE]- Notice the IP addresses
> Our kind Nodes have addresses in the same network:
>
> ```text
> 172.18.0.2
> 172.18.0.3
> 172.18.0.4
> ```
>
> That's because kind connects its Node containers to the same **Docker network**, allowing the Nodes to communicate.
>
> Pods are different. Regular Pods normally receive their own **unique IP address** from Kubernetes' Pod network, such as:
>
> ```text
> 10.244.0.2
> 10.244.0.3
> 10.244.0.4
> ```
>
> So think of it as **two networking layers** for now:
>
> **Node IPs** identify the Nodes.  
> **Pod IPs** identify individual Pods running within the cluster.
>
> Pod IPs can also change when Pods are replaced, which will become important when we introduce **Services**.

Now look at the `NODE` column.
You'll find that Kubernetes has distributed different system components across our Nodes.

For example, several Control Plane components are running on:

```text
kubernetes-practice-02-control-plane
```

while other components may appear on the Worker Nodes.

> [!TIP] Follow the Node
> The `NODE` column connects a **Pod** to the **Node currently running it**.
>
> This gives us a practical way to explore what's actually happening inside our cluster instead of only looking at its architecture diagram.

---

# Visit the Kubernetes API

Now let's revisit something we've seen before: the **Kubernetes API Server**.

Run:

```bash
kubectl cluster-info
```

There's quite a lot hidden inside these two lines. 

![[kubernetes-kubectl-cluster-info-practice-02-results-terminal.png]]

Let's investigate

---

## The Kubernetes Control Plane Address

Let's start with:

```text
https://127.0.0.1:58672
```

This is the address `kubectl` uses to reach our **Kubernetes API Server**.

Inside the control-plane Node, the API Server listens on its Kubernetes API port: `6443`
But our Node is actually running inside a **Docker container**.

kind therefore exposes that API Server to our computer through a local port, which in this example is: `58672`

The exact local port may be different each time you create the cluster.

---

## Can We Visit It?

Try opening the address from `kubectl cluster-info` in your browser. 
**The API Server requires authentication**, which `kubectl` handles using your kubeconfig, but your browser does not. 
Luckily, `kubectl` gives us another way to explore it: the proxy.

---

# kubectl proxy

Run:

```bash
kubectl proxy
```

Keep this terminal running.
Now open:

```text
http://127.0.0.1:8001/
```

This time, you should receive a response from the Kubernetes API.

> [!NOTE] What's happening?
> `kubectl proxy` creates a **local HTTP endpoint** and uses our existing Kubernetes configuration to communicate with the **API Server** for us.
>
> **Browser - kubectl proxy - API Server**

---
## Explore the API

Try:

```text
http://127.0.0.1:8001/api
```

You should receive JSON describing the API versions available under Kubernetes' core API.

Now continue to:

```text
http://127.0.0.1:8001/api/v1
```

The response suddenly becomes **much larger**.

Here we're looking more directly at the **Kubernetes API that `kubectl` has been using for us all along**. Commands such as `kubectl get pods` are simply convenient ways of interacting with this API without having to make the HTTP requests ourselves.

---

# What About the CoreDNS Address?

`kubectl cluster-info` also gave us this:

```text
https://127.0.0.1:58672/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

This looks intimidating, but we can break it apart:

```text
/api/v1
/namespaces/kube-system
/services/kube-dns:dns
/proxy
```

We've actually seen the component behind this already.
Earlier, `kubectl get pods -A` showed us:

```text
coredns-...
coredns-...
```

CoreDNS provides DNS services inside our Kubernetes cluster.
The `kube-dns` Service provides a stable way for the cluster to reach that DNS functionality.

You can find the Service yourself:

```bash
kubectl get services -n kube-system
```

> [!NOTE]- Command Explanation
> `-n` is short for **`--namespace`**. It tells `kubectl` which Kubernetes **Namespace** we want to look inside.
>
> `kube-system` is a Namespace Kubernetes uses for many of the **system resources that help the cluster operate**.

Look for: `kube-dns`

It's showing us a Kubernetes API path that can **proxy through the API Server to a Service running inside the cluster**.

---

# Investigating a Pod

Let's start with one of our CoreDNS Pods.
First, find its name:

```bash
kubectl get pods -n kube-system 
```

You'll see something similar to:

```text
NAME                       READY   STATUS    RESTARTS   AGE
coredns-559f6c778d-l5x8l   1/1     Running   0          20h
coredns-559f6c778d-x8k2p   1/1     Running   0          20h
```

The exact Pod names will be different on your cluster.

Now run:

```bash
kubectl describe pod <pod-name> -n kube-system
```

You'll suddenly get **a lot of information**.
Don't worry about understanding all of it yet. For now, we're only interested in a few things.

Near the top, find:

```text
Node:    kubernetes-practice-02-control-plane/172.18.0.3
```

This tells us **which Node is currently running the Pod**.

We've already seen this information with `kubectl get pods -A -o wide`, but now we're looking at one Pod in much more detail.

Find the `Containers` section:

![[kubernetes-kubectl-describe-pod-coredns-containers-results-terminal.png]]

Remember that a **Pod contains one or more containers**.

Here we can see that our CoreDNS Pod contains a container called `coredns`, as well as the **container image** used to create it.

Continue a little further:

![[kubernetes-kubectl-describe-pod-coredns-containers-state-results-terminal.png]]

We can see values that give us a quick health check:
- **State: Running** - the container is currently running.
- **Ready: True** - Kubernetes considers it ready to do its job.
- **Restart Count: 0** - the container hasn't needed to restart.

So we've gone beyond simply seeing that the Pod exists.

We've inspected it and confirmed that **its container is actually running and ready**.

> [!TIP] get vs describe
> Think of `kubectl get` as our **overview**.
> `kubectl describe` lets us investigate **one resource in much more detail**.
>
> You don't need to understand every field that `describe` gives you yet. We'll encounter many of them naturally as we continue working with Kubernetes.

---
## What is the Pod actually running?

We've confirmed that our CoreDNS Pod is running, and we've seen the container inside it. Now let's take a step back and figure out where this Pod fits into our Kubernetes workload.

Go back toward the top of `kubectl describe` and look for:

![[kubernetes-kubectl-describe-pod-coredns-controlled-by-results-terminal.png]]

Find out where the CoreDNS is located:

```shell
kubectl get pods -n kube-system -o wide
```

![[kubernetes-kubectl-get-pots-namespace-kube-system-results-coredns-location-terminal.png]]

There it is: `kubernetes-practice-02-control-plane`

**So now you might be thinking:** But wait... we never created a CoreDNS Deployment or ReplicaSet ourselves. So where did it come from?

Our `yaml` config was quite simple after all:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

But this is all handled by kind which uses kubeadm.

> [!NOTE]- documentation
> We never created CoreDNS ourselves. **kind uses `kubeadm` to set up the Kubernetes cluster**, and during that setup `kubeadm` creates the CoreDNS Deployment for us.
>
> Kubernetes then creates its **ReplicaSet and Pods**, which is why we're finding them here.
> 
> Read more: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-reconfigure/#updating-the-coredns-deployment-and-service

**Moving on**
Our Pod is being managed by a **ReplicaSet** called `coredns-559f6c778d`.

Let's find it:

```bash
kubectl get replicasets -n kube-system
```

You should find something similar to:

```text
NAME                 DESIRED   CURRENT   READY
coredns-559f6c778d   2         2         2
```

The ReplicaSet wants **2 Pods**, 2 currently exist, and both are ready.

But now we have another question: **Who created and manages this ReplicaSet?**
Let's investigate it just like we investigated our Pod:

```bash
kubectl describe replicaset coredns-559f6c778d -n kube-system
```

Near the top, look for:

```text
Controlled By:  Deployment/coredns
```

There it is.

Our Pod pointed us to its **ReplicaSet**, and the ReplicaSet has now pointed us to its **Deployment**:

```text
Deployment/coredns
	ReplicaSet/coredns-559f6c778d
		Pod/coredns-559f6c778d-l5x8l
        
		Pod/coredns-559f6c778d-p6l9c
```

We've followed the ownership chain backwards from a running Pod to the Deployment responsible for it.

The container itself is running the **CoreDNS software** using the image we saw earlier:

```text
Image: registry.k8s.io/coredns/coredns:v1.14.6
```

**And..?**
We started with a CoreDNS Pod that we knew almost nothing about and investigated it piece by piece, finding where it runs, what is managing it, and what is actually running inside it.

Don't worry if all of those relationships still feel like a lot to remember. The important part is that the architecture we've learned isn't just theoretical anymore.

Instead we've now found it running inside our own cluster.

> [!NOTE] So why does CoreDNS need Pods?
> **CoreDNS is a program that provides DNS for our cluster.** Like any other containerized application managed by Kubernetes, it needs to run inside a **Pod**.
>
> Right now, CoreDNS is simply available and waiting for DNS requests. 
> 
> Later, if `app-one` needs to find `app-two` or a database by its Kubernetes name, **CoreDNS can translate that name into the address needed to reach it**.

---

## What About Our Applications?

Remember these?

```text
app-one/
	app.py

app-two/
	app.py
```

Can we visit **App One** or **App Two** right now?

Let's check if they're available: 

```shell
$ kubectl get pods
$ kubectl get deployments
```

If this returns: 

``` text
No resources found in default namespace.
```

they're **unreachable**.

Our applications are currently unreachable because **they only exist as source code on our computer; Kubernetes hasn't even been told to run them yet.**

---

# What's Next?

Our applications currently exist only as **source code on our computer**.. there are no **Pods or containers running them inside our Kubernetes cluster yet**.

Next, we'll **containerize our applications, publish their images to a container registry, and finally deploy them as workloads inside Kubernetes**.

Continue on: [[Kubernetes - Hands-On Cluster Exploration Practice 03]]