---
icon: LiBook
---
## Table of Contents

- [[#Spec and Status|Spec and Status]]
- [[#Working With Objects|Working With Objects]]
- [[#Namespaces|Namespaces]]
- [[#Labels|Labels]]
- [[#Putting It Together|Putting It Together]]
- [[#What's Next?|What's Next?]]

---

# Overview

![[undraw_file-manager_yics.svg|357]]


Talking about Kubernetes Components is all about the management. But what are all these components we've learned actually managing?

Well, imagine we want Kubernetes to run an application. We couldn't simply say:

> **"Run my application."**

That's nowhere near enough information for Kubernetes.

Instead, let's imagine walking into a room full of people, each with a different responsibility. 
* One coordinates, 
* one keeps track of information,
* another decides where things should go, 
* and others make sure the work gets done. 
Everyone is ready to help, but they're missing one important thing: **what exactly are we asking them to build?**

That's similar to our Kubernetes Components. We know **who does what**, but we still need to describe **what we actually want them to create and manage**. 
That's **Kubernetes Objects**.

Kubernetes needs to know **what we want the cluster to look like**. 
* What container should run? 
* How many copies should exist? 
* What configuration should they use? 
* How should they be organized?

![[kubernetes-objects-purpose-diagram.png|308]]

A **Kubernetes Object** is a representation of something we want Kubernetes to know about and manage in our cluster.

And interestingly, we've already met one:

![[Infrastructure/Kubernetes/res/svg/resources/labeled/pod.svg]]

A **Pod** describes a workload we want Kubernetes to run.

But running applications isn't the only thing we need to describe. 
Different objects represent different parts of what we want our cluster to look like:

 ![[Infrastructure/Kubernetes/res/svg/resources/labeled/deploy.svg]]  **Deployment** (introduced later)

 ![[Infrastructure/Kubernetes/res/svg/resources/labeled/ns.svg]]  **Namespace**

 ![[Infrastructure/Kubernetes/res/svg/resources/labeled/cm.svg ]]  **ConfigMap** (introduced later)

 ![[Infrastructure/Kubernetes/res/svg/resources/labeled/vol.svg]]   **Volume** (introduced later)

So when we create an object, we're essentially telling Kubernetes:

> **"This is something I want to exist or be represented in my cluster."**

Kubernetes stores that information and works to maintain the state we've described.

**And doesn't that sound familiar?**
We've already learned about **desired state** and how controllers continuously work to make the actual state match it. Kubernetes Objects are how we start **describing that desired state**.

---
## Spec and Status

**What does an object actually contain?**
Many Kubernetes Objects follow an important pattern: they describe both **what we want** and **what Kubernetes currently observes**.

Let's talk about **spec** and **status**.
* The **spec** describes the object's **desired state**. 
  It's where we tell Kubernetes what we want to happen.
* The **status** describes the object's **current observed state**. 
  Kubernetes updates it to reflect what is actually happening.

For example, a **Deployment** might have a `spec` saying:

> **"I want 3 replicas of this application."**

Its `status` might then tell us:

> **"3 replicas are currently running and available."**

This gives Kubernetes something very important to compare:

> **Desired State (`spec`) vs Observed State (`status`)**

Not every Kubernetes Object uses `spec` and `status` in exactly this way, but it's a very common pattern that we'll see throughout Kubernetes.

![[kubernetes-objects-spec-status-diagram.png|291]]

Looking at the image above: **the object contains both the desired state and information about the current state.**

If those don't match, Kubernetes can recognize that there's work to do.
And remember the **Controller Manager**?

![[c-m.svg|150]]

Its controllers continuously work to bring the **current state** closer to the **desired state**.

So these ideas aren't separate from Kubernetes Objects. 
They're a fundamental part of how Kubernetes uses them.

---

## Working With Objects

So how do we actually create, inspect, update or delete these objects?

Remember the **API Server**? 

![[api.svg|150]]

We described it as the front door to Kubernetes and its functions. 
This is where that component starts becoming very useful.

When we work with Kubernetes Objects, we're communicating with the **Kubernetes API**. Most commonly, we'll do this using the command-line tool **`kubectl`**, which we'll cover later.

![[kubernetes-objects-kubectl-api-access-diagram.png|508]]


For example, later we might ask Kubernetes which Pods currently exist:

```bash
kubectl get pods
```

`kubectl` sends the appropriate request to the Kubernetes API on our behalf.
It's giving us a convenient way to **talk to the API Server**.


---

## Namespaces

As a cluster grows, we may have multiple:
* applications, 
* projects, 
* teams,
* users.

They may be sharing the same Kubernetes cluster. 
Putting everything together without any organization would quickly become messy.

![[kubernetes-objects-without-namespace-diagram.png]]

**Namespaces** give us a way to logically separate resources within the same physical cluster.
For example, we could have different namespaces for different teams:

![[kubernetes-objects-with-namespace-diagram.png]]

These are still part of the **same Kubernetes cluster**. The namespace simply provides a logical boundary that helps us organize and separate its resources.

**Kubernetes also creates several namespaces automatically.** 
One important example is **`kube-system`**, which is used for Kubernetes system resources, while user applications can exist in namespaces such as **`default`** or ones we create ourselves.

![[kubernetes-objects-default-namespace-diagram.png|514]]

**Namespaces also affect how objects are named.** 
Two Pods cannot have the same name within the same namespace, 
![[kubernetes-objects-pods-cant-share-name-namespace-diagram.png|482]]

but the same Pod name can exist in two different namespaces.

![[kubernetes-objects-pods-namespace-with-identically-named-apps-diagram.png|455]]

So namespaces don't create completely separate Kubernetes clusters. They give us **logical separation inside one cluster**.


---

## Labels

Namespaces help separate resources, but sometimes we want to **group related objects together** without separating them.

That's where **Labels** come in.

A label is simply a **key-value pair** attached to a Kubernetes Object.

For example:

```text
app = webshop
environment = production
```

We could have several Pods with completely different names but give all of them the same `app=webshop` label.

![[kubernetes-objects-labels-key-pair-value-diagram.png|429]]

Unlike an object's name, a label **doesn't need to be unique**. 
In fact, that's the point: multiple objects can share the same label so we can treat them as a group.

However, this raises another question: **how do we actually select that group?**

A **Label Selector** lets Kubernetes find objects based on their labels.

If several Pods have:

```text
app = webshop
```

we can use that label to identify the entire group.

**We could then retrieve all Pods with that label through the Kubernetes API using:**  
`kubectl get pods -l app=webshop`

![[kubernetes-objects-labels-get-pods-kubectl-terminal-command-diagram.png]]

This becomes extremely useful because Kubernetes often needs to work with **groups of objects rather than one specific object**.

And there's another nice connection here: remember the **controllers** we learned about earlier? Controllers can use selectors to identify which objects they are responsible for managing.

So labels tell us **how objects are grouped**, while selectors give us a way to **find that group**.


---

# Putting It Together

We've now moved from understanding **how Kubernetes itself works** to understanding the things Kubernetes actually manages.

A **Kubernetes Object** represents something we want to exist in the cluster. Its **spec** describes the desired state, while its **status** describes the current state. We interact with these objects through the **Kubernetes API**, commonly using `kubectl`.

**Namespaces** provide logical separation and naming scope within a cluster, while **Labels** let us attach identifying information to objects. **Label Selectors** then allow Kubernetes to find groups of objects sharing those labels.

And notice how much of this connects back to the architecture we just learned: the **API Server** gives us access to these objects, **etcd** stores cluster state, and **controllers** continuously work to make the actual state match the desired state.

We're no longer just looking at Kubernetes' machinery. We're starting to learn **how we actually tell that machinery what we want it to do**.


---

# What's Next?

We've spent a lot of time understanding **how Kubernetes thinks and works**.
Such as: exploring the components that make up a cluster, learning how Kubernetes represents things as **Objects**, and how concepts like **desired state, namespaces, labels and selectors** help Kubernetes organize and manage them.

That's a lot of groundwork, but now it starts coming together.
Because ultimately, we want Kubernetes to **run and manage our applications**.

To understand how it does that, we'll start with the simplest workload we've already encountered, the **Pod**, and build from there into: 
1. **ReplicaSets** and 
2. **Deployments**.

Continue on: [[Kubernetes - Kubernetes Workloads (theory)]]