---
icon: LiBook
---
## Table of Contents

- [[#Pods|Pods]]
  - [[#YAML|YAML]]
  - [[#Defining a Pod|Defining a Pod]]
- [[#ReplicaSets|ReplicaSets]]
  - [[#Pod Template|Pod Template]]
  - [[#Labels|Labels]]
  - [[#Selector|Selector]]
  - [[#Putting the ReplicaSet Together|Putting the ReplicaSet Together]]
- [[#Deployments|Deployments]]
- [[#Rolling Updates|Rolling Updates]]
- [[#What's Next?|What's Next?]]

---
# Overview

![[undraw_server-cluster_7ugi.svg|465]]

We've learned that **Kubernetes Objects** describe what we want Kubernetes to create and manage.

Now we'll focus on objects used to run and manage our applications, also known as **workloads**.

We'll build our understanding step by step:
1. **Pod** - runs our application
2. **ReplicaSet** - maintains multiple Pods
3. **Deployment** - manages ReplicaSets and updates

Each solves a problem left by the one before it.

Let's start with the **Pod**.

---

# Pods

Previously, we looked at Pods as part of the **architecture**. Now we're looking at them as **Kubernetes Objects**: something we can actually describe and ask Kubernetes to create.

So how do we describe the Pod we want?

For that, we'll use something called **YAML**.

---

## YAML

Before we start creating Kubernetes resources, we need to briefly introduce **YAML**.

![[yaml.svg|172]]

YAML is a human-readable format used to describe **structured data**. 
Kubernetes commonly uses YAML files to describe the objects we want to create and how they should be configured.

```yaml
name: nginx
replicas: 3
```

Think of YAML as a way of **writing down the configuration we want Kubernetes to understand**.


---

## Defining a Pod

We now know that a Pod represents **one running instance of our application**.
However, Kubernetes still needs to know what that Pod should contain.

When working with Kubernetes, we can describe this using a **YAML file**:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx

spec:
  containers:
     name: nginx
     image: nginx
     ports:
       containerPort: 80
```

This file is essentially our **description of the Pod we want Kubernetes to create**.

`kind` tells Kubernetes **what type of object we're describing**:
	`kind: pod`

`metadata` contains extra information that identifies the object. 
Here, we're simply giving our Pod a name:

```yaml
metadata:
  name: nginx
```

Then we have the `spec`, short for **specification**:

```yaml
spec:
  containers:
    name: nginx
	image: nginx
```

Remember that the `spec` AKA **specification** describes **what we want for this object**.

Because this object is a **Pod**, its spec describes things such as the containers we want inside that Pod. We're simply describing **one Pod and what should run inside it**, which leaves us with an interesting limitation: What if we want three copies of this application instead of one?


---

# ReplicaSets

We've just discovered a limitation of creating a Pod directly:

> **One Pod describes one running instance. What if we want three?**

We could create three separate Pod objects ourselves, but then **we** would be responsible for keeping track of them.

We *could* create three separate Pod objects:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-1
spec:
  containers:
    - name: nginx
      image: nginx

apiVersion: v1
kind: Pod
metadata:
  name: nginx-2
spec:
  containers:
    - name: nginx
      image: nginx

apiVersion: v1
kind: Pod
metadata:
  name: nginx-3
spec:
  containers:
    - name: nginx
      image: nginx
```

This would give us three Pods, but they're **three independently created objects**.
If one disappears, nothing about these Pod definitions says:

> **"There should always be three."**

**WIth ReplicaSet**
Instead, we can describe the result we actually want:

```yaml
apiVersion: apps/v1
kind: ReplicaSet

spec:
  replicas: 3
```

Now we're expressing a **desired number of Pods**.

**Summary**
A **ReplicaSet** is a Kubernetes Object whose job is to **maintain a specific number of identical Pods**.

So we now have an important distinction:

> **Pod:** describes one application instance.  
> **ReplicaSet:** maintains how many copies should exist.

**Moving on**
However, there's something important missing.
We've told the ReplicaSet **how many** Pods we want...

**Three of what?**

---

## Pod Template

The ReplicaSet needs a description of **what the Pods it creates should look like**.

![[kubernetes-pods-replicaset-missing-template-diagram.png|591]]

That's the purpose of the **Pod template**.

The template acts like a **blueprint for new Pods** so that if the ReplicaSet needs another Pod to reach its desired number, Kubernetes uses this template to create it.

We define that template inside the ReplicaSet:

```yaml
apiVersion: apps/v1
kind: ReplicaSet

spec:
  replicas: 3

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
```


---

## Labels

Notice that the template also gives each Pod a label:

```yaml
labels:
  app: nginx
```

We've seen labels before, but here they have an important job.
The ReplicaSet needs a way to identify **which Pods belong to the group it should maintain**.
	**Answer**: the Selector 

---

## Selector

We add the selector to the ReplicaSet's `spec`:

```yaml
apiVersion: apps/v1
kind: ReplicaSet

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
```

The **template** gives the Pods the label `app=nginx`, while the **selector** tells the ReplicaSet to manage Pods with `app=nginx`.

So now our ReplicaSet knows:
> **Replicas:** How many Pods should exist?
> **Template:** What should those Pods look like?
> **Selector:** Which Pods should it manage?

The **selector is required** for a ReplicaSet. Kubernetes needs it to know which Pods should count toward the desired number of replicas.

The selector must also match the labels defined in the Pod template. 
In our example, both use: `app=nginx`
This connects the Pods created from the template to the ReplicaSet that manages them.

---

## Putting the ReplicaSet Together

And now the YAML starts to make sense:

```yaml
apiVersion: apps/v1
kind: ReplicaSet

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
```

Rather than seeing this as a collection of unfamiliar YAML fields, we can now read what we're asking Kubernetes to do:

> **"Maintain 3 Pods matching `app=nginx`.**
> **When you need to create one, use this Pod template."**

So a ReplicaSet solves our original problem: **we can describe how many copies of our application should be running, and Kubernetes works to maintain that number.**

But running the application is only part of its lifecycle.
Eventually, we'll want to **change it**.

Imagine our three Pods are running one version of our application:
* Pod - nginx:v1
* Pod - nginx:v1
* Pod - nginx:v1

Now we release `nginx:v2`.
We don't just want three Pods anymore. We want to **replace the old version with the new version while managing that change safely**.

This introduces a new responsibility: **Maintaining replicas and managing application updates are different jobs.**

The **ReplicaSet** handles the first.
For the second, Kubernetes gives us another object: the **Deployment**.

---

# Deployments

![[Infrastructure/Kubernetes/res/svg/resources/labeled/deploy.svg|150]]

A **Deployment** manages how our application is deployed and updated.
Instead of managing Pods directly, a Deployment manages **ReplicaSets**, which continue doing the job we just learned: maintaining the desired number of Pods.

So our hierarchy becomes:

![[kubernetes-deploy-replicaset-pod-diagram.png|285]]

This is why, for this kind of workload, we normally create a **Deployment** rather than creating the ReplicaSet ourselves.

Interestingly, a basic Deployment definition looks very similar to the ReplicaSet we just saw:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
```

Look at what we already recognize:
* `kind: Deployment` tells Kubernetes which object we're creating.
* `replicas: 3` describes how many replicas we want.
* `selector` identifies the Pods associated with the workload.
* `template` describes the Pods that should be created.

The Deployment uses this information to manage a **ReplicaSet**, and the ReplicaSet maintains the desired number of **Pods**.

But Deployments give us something important beyond simply keeping three Pods alive.
They can manage **updates**.

---

# Rolling Updates

![[update-software.png|184]]

Imagine our three Pods are currently running version `v1` of our application.
Now we release `v2`.

We don't necessarily want to destroy every `v1` Pod at once and then start `v2`. That could temporarily leave our application unavailable.

A **Deployment** can instead perform a **rolling update**, gradually replacing the old version with the new one.

> This is similar to what we covered in [[Docker - Scaling a Swarm Service]] :![[docker-swarm-back-to-the-past-rolling-updates.png]]

Behind the scenes, the Deployment can manage the ReplicaSets involved in this transition, scaling the new version up while scaling the old version down.

This gives us a useful separation of responsibilities:

> **Pod:** runs an instance of our application.  
> **ReplicaSet:** maintains the desired number of Pods.  
> **Deployment:** manages ReplicaSets and application updates.

And that's why **Deployments** are usually the object we'll work with directly when running replicated stateless applications.

---

# What's Next?

So far, we've learned **what Kubernetes is and how it works**. Next, we'll set up a **local Kubernetes environment** on Windows, macOS or Linux so we can start creating and running our own workloads.

Continue on: [[Kubernetes - Installation]]