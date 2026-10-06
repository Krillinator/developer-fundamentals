---
icon: ☑
---
# Quiz

![[quiz-test-img.jpg|361]]


# Quiz

Before moving on, test what you've learned about how **kind, Kubernetes, Deployments, ReplicaSets, Pods and Services work together**.

---

### 1. You create a cluster using:

```bash
kind create cluster --name practice
```

You then run:

```bash
docker ps
```

and notice a container called `practice-control-plane`.

Which explanation best describes what is happening?

- [ ] Kubernetes was installed directly inside your current project directory with kind.
- [ ] kind creates Docker containers that act as Kubernetes Nodes. These Nodes make up the local Kubernetes cluster.
- [ ] Every Kubernetes Pod becomes a Docker container directly on your Mac through kind.
- [ ] Docker is running inside Kubernetes and creates the cluster using kind.

---

### 2. Consider this part of a Deployment:

```yaml
selector:
  matchLabels:
    app: app-two

template:
  metadata:
    labels:
      app: app-two
```

Why do these labels need to match?

- [ ] The first names the Deployment and the second names the Pods.
- [ ] The Deployment uses its selector to identify the Pods belonging to it, while the template gives newly created Pods that label.
- [ ] Kubernetes automatically combines both labels into the Pod name.
- [ ] The Service requires every Deployment to contain two identical labels.

---

### 3. Your Deployment creates three Pods with:

```yaml
labels:
  app: app-two
```

Your Service contains:

```yaml
selector:
  app: app-one
```

What will happen?

- [ ] Kubernetes automatically changes the Service selector to `app-two`.
- [ ] The Service finds the Deployment first and then discovers its Pods.
- [ ] The Service will not select those Pods because their labels do not match its selector.
- [ ] The Pods will fail to start.

---

### 4. Consider this Deployment:

```yaml
spec:
  replicas: 3

  template:
    spec:
      containers:
        - name: application
          image: example/app:1.0

        - name: helper
          image: example/helper:1.0
```

How many Pods and containers should this Deployment result in?

- [ ] 3 Pods with 1 container each
- [ ] 3 Pods with 2 containers each
- [ ] 6 Pods with 1 container each
- [ ] 2 Pods with 3 containers each

---

### 5. A Deployment has:

```yaml
replicas: 3
```

All three Pods are running. One Pod is then deleted.

What should happen?

- [ ] Nothing. Kubernetes only creates Pods when the Deployment is first applied.
- [ ] The Service creates a replacement Pod.
- [ ] The ReplicaSet works to restore the desired number of Pods back to three.
- [ ] The entire Deployment is recreated.

---

### 6. Which description best represents how these resources work together?

- [ ] Deployment creates a Service, which creates a ReplicaSet, which creates Pods.
- [ ] Deployment manages a ReplicaSet, which maintains the desired Pods, while a Service can provide network access to matching Pods.
- [ ] Service manages a Deployment, which distributes containers between ReplicaSets.
- [ ] Pods manage their ReplicaSet and request new replicas when necessary.

---

### 7. Consider these two manifests:

**Deployment:**

```yaml
template:
  metadata:
    labels:
      app: app-two
```

**Service:**

```yaml
spec:
  selector:
    app: app-two
```

The Pods have generated names such as:

```text
app-two-d9d7c77df-knng7
app-two-d9d7c77df-s2d92
app-two-d9d7c77df-vmr7n
```

How can the Service still find all three Pods?

- [ ] It searches for Pods whose names begin with `app-two`.
- [ ] It asks the Deployment for the names of its Pods.
- [ ] It selects Pods using their `app: app-two` label, so their individual names do not matter.
- [ ] Kubernetes automatically gives every Service a list of Pod names.

---

### 8. Consider this Service:

```yaml
spec:
  selector:
    app: app-two

  ports:
    - port: 80
      targetPort: 5000
```

The matching Pods run an application on port `5000`.

What does this configuration mean?

- [ ] The Service receives traffic on port 80 and sends it to port 5000 on matching Pods.
- [ ] The Pods receive traffic on port 80 and send it back through port 5000.
- [ ] Kubernetes changes the application's port from 5000 to 80.
- [ ] The Service is available on `localhost:80`.

---

### 9. You create this file:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: app-two

spec:
  selector:
    app: app-two
```

You save it as `service.yaml`, but:

```bash
kubectl get services
```

does not show `app-two`.

Why?

- [ ] Kubernetes only searches for files named `kubernetes.yaml`.
- [ ] Saving a manifest describes the object, but it must still be applied to the cluster.
- [ ] Services cannot exist until `kubectl port-forward` is running.
- [ ] The Service must be placed inside the kind container manually.

---

### 10. Your application has the following relationship:

A Deployment manages a ReplicaSet, which maintains three Pods. 
A Service then uses the `app: app-two` label to select those Pods.

One Pod is replaced and receives a completely different generated name.

Which statement is true?

- [ ] The Service remembers the old Pod and renames the new one.
- [ ] The Service communicates only with the ReplicaSet.
- [ ] The replacement Pod receives the expected label, so it still matches the Service selector.
- [ ] Services ignore Pod replacements.

---

**See how you did below.**

> [!SUCCESS]- Quiz Answers
>
> ### 1. kind uses Docker containers as Kubernetes Nodes, and the Kubernetes cluster runs using those Nodes.
>
> - [ ] Kubernetes was installed directly inside your current project directory.
> - [x] kind creates Docker containers that act as Kubernetes Nodes. These Nodes make up the local Kubernetes cluster.
> - [ ] Every Kubernetes Pod becomes a Docker container directly on your Mac.
> - [ ] Docker is running inside Kubernetes and creates the cluster.
>
> kind runs Kubernetes Nodes as Docker containers. In a basic kind cluster, `practice-control-plane` is the Kubernetes Node.
>
> Your project directory is not where the cluster itself lives.
> ![[kubernetes-kind-cluster-hierarchy-with-worker-diagram.png]]
>
> ---
>
> ### 2. The selector identifies the Pods and the template gives them the matching label.
>
> - [ ] The first names the Deployment and the second names the Pods.
> - [x] The Deployment uses its selector to identify the Pods belonging to it, while the template gives newly created Pods that label.
> - [ ] Kubernetes automatically combines both labels into the Pod name.
> - [ ] The Service requires every Deployment to contain two identical labels.
>
> Think of the two sections as:
>
> ```text
> selector        What Pods should belong here?
> template.labels What labels should created Pods receive?
> ```
>
>
> ---
>
> ### 3. The Service will not select the Pods.
>
> - [ ] Kubernetes automatically changes the Service selector to `app-two`.
> - [ ] The Service finds the Deployment first and then discovers its Pods.
> - [x] The Service will not select those Pods because their labels do not match its selector.
> - [ ] The Pods will fail to start.
>
> The Pods can still run normally.
> The **ReplicaSet** is responsible for maintaining the desired number of Pods, while the **Deployment** manages the ReplicaSet.
>
> The problem is that the Service is looking for:
>
> ```yaml
> app: app-one
> ```
>
> while the Pods have:
>
> ```yaml
> app: app-two
> ```
>
> ---
>
> ### 4. 3 Pods with 2 containers each.
>
> - [ ] 3 Pods with 1 container each
> - [x] 3 Pods with 2 containers each
> - [ ] 6 Pods with 1 container each
> - [ ] 2 Pods with 3 containers each
>
> `replicas: 3` refers to the number of **Pods**.
>
> The Pod template contains two containers, so every replica contains both:
>
> ```text
> Pod 1
> - application
> - helper
>
> Pod 2
> - application
> - helper
>
> Pod 3
> - application
> - helper
> ```
>
> ---
>
> ### 5. The ReplicaSet works to restore the desired number of Pods.
>
> - [ ] Nothing. Kubernetes only creates Pods when the Deployment is first applied.
> - [ ] The Service creates a replacement Pod.
> - [x] The ReplicaSet works to restore the desired number of Pods back to three.
> - [ ] The entire Deployment is recreated.
>
> The Deployment manages a ReplicaSet, and the ReplicaSet maintains the desired number of Pod replicas.
>
> ```text
> Deployment
>     │
> ReplicaSet
>     │
> ├── Pod
> ├── Pod
> └── Pod
> ```
>
> If one disappears, the ReplicaSet works to restore the desired state.
>
> ---
>
> ### 6. Deployment, ReplicaSet and Service have different responsibilities.
>
> - [ ] Deployment creates a Service, which creates a ReplicaSet, which creates Pods.
> - [x] Deployment manages a ReplicaSet, which maintains the desired Pods, while a Service can provide network access to matching Pods.
> - [ ] Service manages a Deployment, which distributes containers between ReplicaSets.
> - [ ] Pods manage their ReplicaSet and request new replicas when necessary.
>
> A useful mental model is:
> ![[kubernetes-service-whole-relationship-diagram.png]]
>
> ---
>
> ### 7. The Service finds them using labels.
>
> - [ ] It searches for Pods whose names begin with `app-two`.
> - [ ] It asks the Deployment for the names of its Pods.
> - [x] It selects Pods using their `app: app-two` label, so their individual names do not matter.
> - [ ] Kubernetes automatically gives every Service a list of Pod names.
>
> Pod names can change as Pods are replaced.
>
> Labels provide a stable way to identify a group of Pods.
>
> ---
>
> ### 8. The Service receives traffic on port 80 and sends it to port 5000 on the Pods.
>
> - [x] The Service receives traffic on port 80 and sends it to port 5000 on matching Pods.
> - [ ] The Pods receive traffic on port 80 and send it back through port 5000.
> - [ ] Kubernetes changes the application's port from 5000 to 80.
> - [ ] The Service is available on `localhost:80`.
>
> ```text
> Service :80
>      │
>      ▼
> Pod :5000
> ```
>
> `port` is the Service port.
>
> `targetPort` is the port traffic is sent to on the selected Pods.
>
> ---
>
> ### 9. The manifest hasn't been applied.
>
> - [ ] Kubernetes only searches for files named `kubernetes.yaml`.
> - [x] Saving a manifest describes the object, but it must still be applied to the cluster.
> - [ ] Services cannot exist until `kubectl port-forward` is running.
> - [ ] The Service must be placed inside the kind container manually.
>
> Apply it with:
>
> ```bash
> kubectl apply -f service.yaml
> ```
>
> The YAML file exists on your computer. `kubectl apply` sends the desired configuration to Kubernetes.
>
> ---
>
> ### 10. The replacement Pod still has the expected label.
>
> - [ ] The Service remembers the old Pod and renames the new one.
> - [ ] The Service communicates only with the ReplicaSet.
> - [x] The replacement Pod receives the expected label, so it still matches the Service selector.
> - [ ] Services ignore Pod replacements.
>
> This is one of the important reasons for using labels instead of depending on individual Pod names.
>
> ```text
> old-pod-x7f2    app: app-two
>                     ↓
>                  replaced
>                     ↓
> new-pod-k9m4    app: app-two
>
> Service selector: app: app-two
> ```
>
> The Pod changed. The label relationship did not.

**Passing grade: 80%**

---

# What's Next?

So far, we've mainly worked with Kubernetes using **YAML files** and:

```bash
kubectl apply -f <file>
```

But this isn't the only way to work with Kubernetes.

Kubernetes supports two important approaches:
- **Imperative**: we tell Kubernetes directly what action to perform.
- **Declarative**: we describe the desired state and let Kubernetes determine what needs to change.

In the next section, we'll compare these approaches and see why commands such as:

```bash
kubectl create
```

behave differently from the:

```bash
kubectl apply
```

workflow we've been using so far.

> [!NOTE]
> You don't need to choose one and forget the other. Understanding both will help you recognize different Kubernetes workflows and know when each approach makes sense.

Continue on: [[Kubernetes - Imperative vs Declarative (theory)]]