---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

**We've deployed app-one together.**
This time, you're going to repeat the process with **app-two**, but with less guidance.

By the end, you should be able to access **app-two** from your computer.
Don't worry about remembering every command or every part of the YAML.

---

# 1. Prepare App Two

Move into the `app-two` directory.
You should already have the application and its `Dockerfile` here.

We're going to need two new Kubernetes manifests AKA yaml files for Deployment and Service.
Create the two empty files.

You can copy and paste the Dockerfile as-is from `app-one` into `app-two`.
	Don't forget to Docker build and push to Registry!

---

# 2. Create the Deployment

Let's start with `deployment.yaml`.

Your Deployment should:
- Be named `app-two`
- Run **3 replicas**
- Give its Pods the label `app: app-two`
- Use that label in its selector
- Run your **app-two image**
- Use port `5000`

**Now that's a lot, huh?**
Instead of copying the Deployment we created earlier, let's see how much of its structure we can rediscover using Kubernetes itself.

Start by exploring the deployment, with:

```bash
kubectl explain deployment
```

Near the bottom, you should find the top-level fields of a Deployment:

```text
FIELDS:
  apiVersion
  kind
  metadata
  spec
  status
```

Most of these should look familiar from the Deployment we created earlier, they are in fact identical.

But `kubectl explain` doesn't just show us the fields. It also tells us their **types**, whether Kubernetes considers them **required**, and what they are used for.

For example:

```text
spec    <DeploymentSpec> -required-
    Specification of the desired behavior of the Deployment.
```

So what belongs inside `spec`?

Ask Kubernetes:

```bash
kubectl explain deployment.spec
```

Now you'll discover another level of the structure, including fields such as:

```text
replicas
selector
template
```

Notice that some fields are marked:

```text
-required-
```

These are fields required by that part of the Kubernetes schema.

We can continue exploring deeper:

```bash
kubectl explain deployment.spec.selector
kubectl explain deployment.spec.template
kubectl explain deployment.spec.template.spec
```

> [!TIP] Explore the Resource
> `kubectl explain` lets you investigate the structure of Kubernetes resources directly from your terminal.
>
> You can keep adding fields to the command to explore deeper:
>
> ```bash
> kubectl explain deployment
> kubectl explain deployment.spec
> kubectl explain deployment.spec.template
> kubectl explain deployment.spec.template.spec
> ```
>
> You don't need to memorize the entire YAML structure. Learn how to **find it when you need it**.

**How do we get started with apiVersion?**
There's one piece that isn't immediately obvious.

We know our resource is a:

```yaml
kind: Deployment
```

But what should we put here?

```yaml
apiVersion: ???
```

Kubernetes can help us discover that too.

Run:

```bash
kubectl api-resources
```

This lists the resource types supported by our cluster.

Find `deployments` and look at its **APIVERSION** column.

> [!SUCCESS]- What did you find?
> You should find something similar to:
>
> ```text
> NAME          APIVERSION   NAMESPACED   KIND
> deployments   apps/v1      true         Deployment
> ```
>
> This tells us:
>
> ```yaml
> apiVersion: apps/v1
> kind: Deployment
> ```

---

## Exploring Objects with explain (optional)

We now know that a Deployment contains fields such as:

```text
apiVersion
kind
metadata
spec
```

But knowing that `metadata` exists doesn't yet tell us **how to use it**.

**Consider this**
We need to give our Deployment a name, but where does that name belong?

Take another look at:

```bash
kubectl explain deployment
```

Notice how `metadata` is described as:

```text
metadata    <ObjectMeta>
```

This is an **object**, which means it contains its own fields.

We can explore those fields by going one level deeper:

```bash
kubectl explain deployment.metadata
```

Look through the result and find:

```text
name    <string>
```

We've now discovered where the Deployment's name belongs:

```yaml
metadata:
  name: app-two
```

---

## Understanding matchLabels (optional)

There's one more part of our Deployment that we need to understand before building the manifest.

Explore the selector:

```bash
kubectl explain deployment.spec.selector
```

You'll find:

```text
matchLabels    <map[string]string>
```

So far, we've mostly encountered fields containing individual values, such as:

```yaml
replicas: 3
```

But `matchLabels` is different. Its type is:

```text
map[string]string
```

A **map** stores pairs of **keys and values**.

In YAML, we write a key-value pair like this:

```yaml
key: value
```

For example:

```yaml
app: app-two
```

Here:

```text
app       = key
app-two   = value
```

Since `matchLabels` is the map itself, the pair belongs underneath it:

```yaml
matchLabels:
  app: app-two
```

This tells the Deployment:

> Match Pods whose `app` label has the value `app-two`.

We can use the same key-value structure when we later give those Pods their label:

```yaml
labels:
  app: app-two
```

The selector **looks for** `app: app-two`, while the Pod template **gives the Pods** `app: app-two`.

---

## Understanding Lists in YAML (optional)

When exploring our Pod specification:

```bash
kubectl explain deployment.spec.template.spec
```

we find:

```text
containers    <[]Container> -required-
```

The `[]` means that `containers` is a **list**.

In YAML, each item in a list starts with `-`:

```yaml
containers:
  - name: app-two
    image: <your-image>
```

The `-` simply means **one item in the list**.

If we had two containers:

```yaml
containers:
  - name: app-two
    image: <your-image>

  - name: another-container
    image: <another-image>
```

---
## Build the Manifest

You now have the tools needed to reconstruct the Deployment.

Use:

```bash
kubectl explain deployment
kubectl explain deployment.spec
kubectl explain deployment.spec.template
kubectl api-resources
```

and what you learned from **app-one** to complete `deployment.yaml`.

> [!SUCCESS]- Solution
> Your `deployment.yaml` should look similar to:
>
> ```yaml
> apiVersion: apps/v1
> kind: Deployment
>
> metadata:
>   name: app-two
>
> spec:
>   replicas: 3
>
>   selector:
>     matchLabels:
>       app: app-two
>
>   template:
>     metadata:
>       labels:
>         app: app-two
>
>     spec:
>       containers:
>         - name: app-two
>           image: <your-dockerhub-username>/app-two:1.0
>           ports:
>             - containerPort: 5000
> ```
>
> Replace `<your-dockerhub-username>` with your own Docker Hub username.

---

# 3. Apply the Deployment

Having a `deployment.yaml` file isn't enough.

We need to send that configuration to Kubernetes.

You've used the command for this before. Instead of being given the finished command, start with:

```bash
kubectl --help
```

Find the command described as:

```text
Apply a configuration to a resource by file name or stdin
```

Once you've found it, explore its help:

```text
kubectl <command> --help
```

Find the option used to specify a **filename**.

Then use it with:

```text
deployment.yaml
```

> [!SUCCESS]- Solution
> The command is `apply`, and `-f` specifies the file:
>
> ```bash
> kubectl apply -f deployment.yaml
> ```
>
> You should see something similar to:
>
> ```text
> deployment.apps/app-two created
> ```

---

# 4. Check the Deployment

Before moving on, make sure Kubernetes actually created what we asked for.

You already know a `kubectl` command that can **display resources**.

If you've forgotten it:

```bash
kubectl --help
```

Find the command described as:

```text
Display one or many resources
```

Use it to check:

1. The Deployment
2. The Pods

Can you confirm that **three app-two Pods** are running?

> [!SUCCESS]- Solution
> Check the Deployment:
>
> ```bash
> kubectl get deployments
> ```
>
> Then check the Pods:
>
> ```bash
> kubectl get pods
> ```
>
> You should eventually see `3/3` replicas ready for `app-two` and three Pods belonging to the Deployment.

---

# 5. Create the Service

Our Pods are running, but we still need a stable network endpoint for them.

Open:

```text
service.yaml
```

Your Service needs to:
- Be named `app-two`
- Select Pods with `app: app-two`
- Listen on port `5000`
- Send traffic to port `5000` on the selected Pods

Again, try to build it yourself first.

You can investigate the Service resource with:

```bash
kubectl explain service
kubectl explain service.spec
```

See if you can identify where the **selector** and **ports** belong.

> [!SUCCESS]- Solution
> Your `service.yaml` should look similar to:
>
> ```yaml
> apiVersion: v1
> kind: Service
>
> metadata:
>   name: app-two
>
> spec:
>   selector:
>     app: app-two
>
>   ports:
>     - port: 5000
>       targetPort: 5000
> ```

---

# 6. Create the Service in Kubernetes

You now have another manifest, but remember:

> **Creating the YAML file does not create the Kubernetes object.**

Use the same command you discovered earlier to apply:

```text
service.yaml
```

> [!SUCCESS]- Solution
>
> ```bash
> kubectl apply -f service.yaml
> ```
>
> You should see:
>
> ```text
> service/app-two created
> ```

---
# 7. Find Your Service

Let's verify that the Service exists.

You already know how to retrieve Kubernetes resources.

Figure out how to list the Services in the cluster.

> [!TIP]
> If you've forgotten the resource name Kubernetes expects, these can help:
>
> ```bash
> kubectl get --help
> kubectl api-resources
> ```

> [!SUCCESS]- Solution
>
> ```bash
> kubectl get services
> ```
>
> You should now find both the built-in `kubernetes` Service and your application Services, including:
>
> ```text
> app-two    ClusterIP    ...    5000/TCP
> ```

---

# 8. Access App Two

There's one final problem.

The Service is available **inside the Kubernetes cluster**, but we want to reach it from **our computer**.

We've already used a `kubectl` command that creates a temporary connection between a local port and a resource inside Kubernetes.

Can you find it again?

Start with:

```bash
kubectl --help
```

Look for the command described as:

```text
Forward one or more local ports to a pod
```

Then explore it:

```text
kubectl <command> --help
```

Use the examples to figure out how to forward traffic to the `app-two` Service.
Look for: "Usage:"

> [!SUCCESS]- Solution
> We can use local port `5000`:
>
> ```bash
> kubectl port-forward service/app-two 5000:5000
> ```
>

---

# 9. Test It

Open your browser and figure out which local address should now reach **app-two**.

> [!SUCCESS]- Solution
> Open:
>
> ```text
> http://localhost:5001
> ```
>
> If everything is working, you should now see **app-two** running through Kubernetes.

Once you're done, try out the following command: 

``` shell
kubectl get pods -o wide
```

Notice something interesting?

![[kubectl-get-pods-after-two-deployments-result-terminal.png]]

**The Pods have been distributed across our two worker nodes.**

We didn't manually choose which Node should run each Pod. Kubernetes handled this automatically using the **Scheduler**.

When a new Pod needs somewhere to run, the Scheduler looks at the available Nodes and chooses a suitable one based on things such as **available resources and scheduling constraints**.

---

# What Did We Practice?

This time, you deployed an application without being given every command upfront.

You:
- Created a **Deployment**
- Applied it to Kubernetes
- Verified its **Pods**
- Created a **Service**
- Applied and verified the Service
- Used **port forwarding** to access it from your computer

More importantly, whenever you didn't know exactly what to write or which command to use, you had tools available to investigate:

```bash
kubectl --help
kubectl <command> --help
kubectl explain <resource>
kubectl api-resources
```

The goal is to understand the pieces well enough that you can **find the details when you need them**.

Continue on: [[Kubernetes - Knowledge Check 02]]