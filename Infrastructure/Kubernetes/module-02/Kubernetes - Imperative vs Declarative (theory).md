---
icon: LiBook
---
# Overview

![[undraw_selected-options_2x1i.svg|455]]



So far, we've mostly worked with Kubernetes using configuration files.

For example:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

We described what we wanted in YAML and asked Kubernetes to make the cluster match that configuration.

This is known as a **declarative approach**.

But Kubernetes also allows us to work differently.

## Imperative Commands

With an **imperative command**, we tell Kubernetes directly what we want it to do.

Instead of first writing:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: app-two

spec:
  ...
```

we can create a Deployment directly from the terminal:

```bash
kubectl create deployment app-imperative \
  --image=ayrayen/app-two:1.0
```

Here, we're explicitly telling Kubernetes:

> Create a Deployment called `app-imperative` using this image.

There is no `deployment.yaml` describing this Deployment.

The command itself contains the instruction.

> [!NOTE]
> Imperative commands are useful for quick operations, experimentation, and learning.
>
> The downside is that the command itself becomes important. If another developer wants to reproduce what we did, they need to know which commands and options we used.

---

# Declarative Configuration

The approach we've used until now is **declarative**.

Instead of telling Kubernetes which operation to perform, we describe the **desired state** in a configuration file:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: app-two

spec:
  replicas: 3
  ...
```

Then we apply it:

```bash
kubectl apply -f deployment.yaml
```

We're essentially saying:

> This is what I want the Deployment to look like. Make the cluster match it.

Kubernetes determines what needs to happen.

If the object doesn't exist, it can be created.

If the configuration changes, the existing object can be updated.

```text
Imperative
"I want you to CREATE this."

Declarative
"I want the state to LOOK LIKE this."
```

> [!TIP]
> Declarative configuration also gives us something valuable: the configuration can be stored, reviewed, shared, and version controlled.

---

# Hands-On: Deploy Without YAML

You've already deployed `app-two` declaratively.

Now let's deploy the same application again, but this time **without creating a Deployment or Service manifest**.

We'll give this version a different name so it can exist alongside our current application.

## 1. Create the Deployment

We've previously used:

```bash
kubectl apply
```

This time, explore:

```bash
kubectl create --help
```

Find a way to create a **Deployment** directly.

Your Deployment should:

- be named `app-imperative`
- use `ayrayen/app-two:1.0`

> [!SUCCESS]- Solution
>
> ```bash
> kubectl create deployment app-imperative \
>   --image=ayrayen/app-two:1.0
> ```

---

## 2. Check What Happened

Use the commands you've already learned to inspect the result.

> [!SUCCESS]- Solution
>
> ```bash
> kubectl get deployments
> kubectl get pods
> ```
>
> Kubernetes created the Deployment even though we never created a `deployment.yaml` file.

---

## 3. Scale the Deployment

Our declarative Deployment specified its replicas in YAML:

```yaml
replicas: 3
```

But this Deployment doesn't have our YAML configuration.

Explore:

```bash
kubectl scale --help
```

Can you change `app-imperative` to **3 replicas** directly from the terminal?

> [!SUCCESS]- Solution
>
> ```bash
> kubectl scale deployment app-imperative --replicas=3
> ```
>
> Check the result:
>
> ```bash
> kubectl get pods
> ```

---

## 4. Create a Service

Our previous Service was described using `service.yaml`.

This time, don't create one.

Explore:

```bash
kubectl expose --help
```

Find a way to create a Service for `app-imperative` using port `5000`.

> [!SUCCESS]- Solution
>
> ```bash
> kubectl expose deployment app-imperative \
>   --port=5000 \
>   --target-port=5000
> ```

Now check:

```bash
kubectl get services
```

---

# Compare the Two Approaches

We now have applications created using two different workflows.

### Declarative

```text
deployment.yaml
service.yaml

        ↓

kubectl apply

        ↓

Kubernetes resources
```

### Imperative

```text
kubectl create deployment ...
kubectl scale ...
kubectl expose ...

        ↓

Kubernetes resources
```

Both approaches can result in similar Kubernetes resources.

The difference is **how we described what we wanted**.

With the imperative approach, we told Kubernetes which operations to perform.

With the declarative approach, we stored our desired state in configuration files and let Kubernetes determine the necessary operations.