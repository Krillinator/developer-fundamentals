---
icon: LiRocket
---
## Table of Contents

- [[#Overview|Overview]]
- [[#Preparing Our Application|Preparing Our Application]]
- [[#Containerizing App One|Containerizing App One]]
- [[#Build the Image|Build the Image]]
- [[#Test the Image|Test the Image]]
- [[#Where Does the Image Exist?|Where Does the Image Exist?]]
- [[#Container Registries|Container Registries]]
- [[#Publishing App One|Publishing App One]]
- [[#Make Image Registry Private (optional)|Make Image Registry Private (optional)]]
- [[#From Cluster to Application|From Cluster to Application]]
- [[#Creating Our Deployment|Creating Our Deployment]]
- [[#Deploy App One|Deploy App One]]
- [[#Debugging (optional)|Debugging (optional)]]
- [[#But Can We Visit It?|But Can We Visit It?]]
- [[#Kubernetes Services|Kubernetes Services]]
- [[#Creating Our Service|Creating Our Service]]
- [[#Accessing Our Application from Outside the Cluster|Accessing Our Application from Outside the Cluster]]
- [[#Summary|Summary]]
- [[#What's Next?|What's Next?]]


---
# Overview

![[undraw_code-deployed_iwvu.svg|300]]

We've explored our Kubernetes cluster and found the system workloads already running inside it. Now it's time to get **our own applications running**.

At the moment, `app-one` and `app-two` are only source code on our computer:

![[kubernetes-practice-02-folder-structure.png|181]]

Kubernetes hasn't been given anything it can run yet.

In this module, we'll take our applications **from source code on our computer to running containers inside Pods on our Kubernetes Worker Nodes.**

---

# Preparing Our Application

We know where we want `app-one` to end up: **running inside a container, inside a Pod, on our Kubernetes cluster**.

But right now we only have:

```text
app-one/
	app.py
```

Before Kubernetes can run our application, we first need a **container image**. 
And to build that image, we're missing something familiar from Docker: **a Dockerfile.**

![[kubernetes-practice-02-dockerfile-directory-hierarchy.png|256]]

---

# Containerizing App One

Let's begin by creating a `Dockerfile`.

Open the Dockerfile and add:

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY app.py .

RUN pip install flask

EXPOSE 5000

CMD ["python", "app.py"]
```

Most of this should be familiar from Docker. 
We're creating an environment with Python, copying our application into it, installing Flask and telling the container how to start the application.


---

# Build the Image

Move into the application directory:

```bash
cd app-one
```

Then build the image:

```bash
docker build -t app-one:1.0 .
```

The `-t` option gives our image a name (app-one) and tag (1.0).

Once the build finishes, verify that the image exists:

```bash
docker images
```

You should now find:

```text
app-one    1.0
```

Now we have converted source code to a Docker Image.

---

# Test the Image

Before publishing or deploying anything, let's make sure the image actually works.

Run:

```bash
docker run --rm -p 5000:5000 app-one:1.0
```

> [!NOTE]- What does rm do?  
> `--rm` tells Docker to **automatically remove the container when it stops**.
> 
> The **image is not deleted**. 
> Only the container created from it is removed.
> 

> [!WARNING]- Development server warning 
> Flask's built-in server is intended for **development and testing**, not production.
> 
> For production, the application should run behind a **production WSGI server**, such as Gunicorn.
> 
> ```
> gunicorn app:app
> ```
> 
> The development server is convenient, but isn't designed for production-level **security, reliability, or performance**.
> This is fine for now, we're not building a production-level application for our test.

Then visit:

```text
http://localhost:5000
```

You should see:

![[kubernetes-docker-image-hello-from-app-one-website-result.png|472]]

**Great!**
We now know that our image can successfully run the application.

Stop the container with:

```text
Ctrl+C
```

---

# Where Does the Image Exist?

There's an important detail here.

When we ran:

```bash
docker build -t app-one:1.0 .
```

we created the image **locally on our computer**.
Our Kubernetes cluster doesn't automatically get access to every image we build locally.

Because we're using **kind**, we could load the image directly into our kind Nodes.
That works well for local development. But we're going to use this opportunity to follow a workflow much closer to what we'd use with a real Kubernetes environment. 

---

# Container Registries

This should sound familiar, this is because we actually ended up covering this in:
	Docker - Module 02: [[Docker - Push to Central Registry]]

Remember the CoreDNS image:

```text
registry.k8s.io/coredns/coredns:v1.14.6
```

That image isn't coming from someone's local Docker installation. It's stored in a **container registry**.

A container registry provides a place where container images can be published and retrieved:

Instead of making our Kubernetes cluster depend on the Docker images stored locally on our computer, we'll publish our application image to a registry.

Kubernetes can then retrieve it when it needs to create our containers.

---

# Publishing App One

Before pushing the image, it needs a name that identifies where it should be stored.

Log in to Docker:

``` shell
docker login
```

Make sure you rename the docker image so it follows the Docker Registry standards.

``` shell
docker tag app-one:1.0 my-username/app-one:1.0
```

publish to Docker Registry

``` shell
docker push my-username/app-one:1.0
```

Now our image exists somewhere our Kubernetes Nodes can retrieve it.

Remember, we've done all these steps previously in: 
	Docker - Module 02: [[Docker - Push to Central Registry]]


---
# Make Image Registry: Private (optional)

If you want to debug later, make your registry private on the website.
This is great for learning the basics debugging within Kubernetes.

---
# From Cluster to Application

So far, we've worked with two different things:
- **kind** created our Kubernetes cluster
- **Docker** built and published our application image

But our Kubernetes cluster still doesn't know that we want to run `app-one`.

This is where the Kubernetes Object: **Deployment** comes in.

![[Infrastructure/Kubernetes/res/svg/resources/labeled/deploy.svg|150]]

A Deployment describes how we want Kubernetes to run our application. Among other things, it tells Kubernetes:
- which container image to use
- how many replicas we want
- how the Pods should be identified

We describe this in a Kubernetes manifest, commonly stored in a file such as:
	`deployment.yaml`

---
# Creating Our Deployment

Navigate to the project root and create a `deployment.yaml`file.

Our application now contains:
* app.py
* Dockerfile
* deployment.yaml

![[kubernetes-app-one-folder-deployment-file-hierarchy.png|202]]

Open `deployment.yaml`:

``` yaml
apiVersion: apps/v1  # Kubernetes API for Deployments
kind: Deployment

metadata:
  name: app-one # Name of the Deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: app-one # Select Pods with this label

  template:
    metadata:
      labels:
        app: app-one # Label given to the Pods

    spec:
      containers:
        - name: app-one # Name of the container
          image: <your-published-image>
          ports:
            - containerPort: 5000
```

This should look familiar from our previous Kubernetes modules.
The important part for us right now is:

```yaml
image: <your-published-image>
```

This tells Kubernetes **which container image should be used to create the container**.

---

# Deploy App One

Give the manifest to Kubernetes:

```bash
kubectl apply -f app-one/deployment.yaml
```

> [!TIP]- What does -f mean?
> Not sure what an option like `-f` does? Ask `kubectl`:
> ```bash
> kubectl apply -h
> ```
> This shows the available options for `apply`, including what `-f` is used for.

> "Apply a configuration to a resource by file name or stdin"

`kubectl apply` makes the cluster match the configuration in the file. For example, if you change `replicas` from `3` to `5` and run `apply` again, Kubernetes updates the Deployment to use **5 replicas**.

---

# Debugging (optional)

First off, let's debug and check out the state of our pods.

Check Deployment:

```bash
kubectl get deployments
```

Then check the Pods:

```bash
kubectl get pods
```

**Did you notice it?**
It should now say something along the lines of `0/3`. 
Nothing is running, but why?

Let's try debugging by describing one of the pods.

``` shell
kubectl describe pod app-one-74d65c9f59-2cgpv
```

Now look at the bottom: events

> Failed to pull image "my-name/app-one:1.0": failed to pull and unpack image "docker.io": failed to resolve reference "etc...": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed

There are two solutions here: either we access it using authorization, or we make it publicly available.

Let's try accessing it using authorization.

Create the secret:

``` shell
kubectl create secret docker-registry dockerhub-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<your-dockerhub-username> \
  --docker-password=<your-dockerhub-access-token>
```

> [!WARNING] Secrets are not automatically encrypted
> Kubernetes `Secret` values are **encoded, not necessarily encrypted**.
>
> Also be careful when passing credentials directly in a command, as they may be stored in your **shell history**.

Tell deployment to use it:

``` yaml
template:
  metadata:
    labels:
      app: app-one

  spec:
    imagePullSecrets:
      - name: dockerhub-secret # NEW

    containers:
      - name: app-one
        image: my-name/app-one:1.0
        ports:
          - containerPort: 5000
```

---
# But Can We Visit It?

Our Pods may now be running, but try visiting:

```text
http://localhost:5000
```

It doesn't work.

This time, the reason is different.

Earlier, the application wasn't running at all. Now it **is running inside our Pods**, but we haven't created a way to reach those Pods from our computer.

You might remember that our Deployment contains:

```yaml
ports:
  - containerPort: 5000
```

So haven't we already exposed port `5000`?
**Not quite.**

`containerPort` tells Kubernetes that our container is expected to listen on port `5000`. It does **not** publish that port on our computer like Docker's `-p` option did.

Remember when we ran our container with Docker:

```bash
docker run --rm -p 5000:5000 app-one:1.0
```

The `-p` created a connection between a port on our computer and a port inside the container.

With Kubernetes, we need to create that connection differently.

This introduces another Kubernetes object: a **Service**.

---

# Kubernetes Services

![[Infrastructure/Kubernetes/res/svg/resources/labeled/svc.svg|150]]

A **Service** provides a stable way to reach a group of Pods.

This becomes especially important with our Deployment because we currently have three replicas: `3/3`.

Rather than trying to connect to one particular Pod, we can place a Service in front of them:

![[kubernetes-service-diagram.png|299]]

So.. how does the Service know **which Pods belong to `app-one`?**

We've actually already given Kubernetes the information it needs.

Remember the labels in our Deployment?

```yaml
template:
  metadata:
    labels:
      app: app-one
```

Every Pod created by this Deployment receives:

```yaml
app: app-one
```

A Service can use a **selector** to find Pods with that label:

```yaml
selector:
  app: app-one
```

The Service doesn't need to know the individual names of our Pods.
It simply looks for Pods matching its selector.

> [!NOTE] Why is this useful?
> Pods can be created, removed and replaced over time.
>
> Their individual names may change, but as long as the replacement Pods have the label `app: app-one`, the Service can still find them.

---
# Creating Our Service

Just like our Deployment, a Service is a **Kubernetes object**.

For our Deployment, we described the object we wanted in `deployment.yaml` and then used `kubectl apply` to create it in our cluster.

![[kubernetes-deploy-yaml-replicaset-pods-relationship-diagram.png]]

We'll do the same thing for our Service.

The Service needs to describe two important things:
- **which Pods it should send traffic to**
- **which ports should be used**

![[kubernetes-service-yaml-client-pods-relationship-diagram.png|546]]

We'll describe this in another Kubernetes manifest.

Create: `service.yaml`

Our application now contains:

![[kubernetes-practice-02-dockerfile-directory-3-hierarchy.png|292]]

Open `service.yaml`:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: app-one

spec:
  selector:
    app: app-one

  ports:
    - port: 5000
      targetPort: 5000
```

**Let's look at what we're asking Kubernetes to create.**
First, our selector tells the Service **which Pods it belongs in front of**.
Remember that our Deployment gives its Pods this label:

```yaml
labels:
  app: app-one
```

The Service uses that same label to find them.

Then we have:

```yaml
ports:
  - port: 5000
    targetPort: 5000
```

`port` is the port the **Service listens on**.

`targetPort` is the port the **application inside our Pods listens on**.

```text
Service :5000 -> Pod :5000
```

So this manifest describes a Service that listens on port `5000`, finds Pods labeled `app: app-one`, and forwards traffic to port `5000` on those Pods.

We now have the configuration we need to create our Service.

Apply it:

```shell
kubectl apply -f service.yaml
```

Verify it with:

``` shell
kubectl get services
```

---

# Accessing Our Application from Outside the Cluster

Our application is now running behind a Service, but there's an important boundary we yet to cross.

Everything we've created so far exists **inside our Kubernetes cluster**.
The Service gives the Pods a stable network endpoint, but by default that endpoint is intended for communication **inside the cluster**.

When we added the following configuration to `service.yaml`

```yaml
ports:
  - port: 5000
    targetPort: 5000
```

we made the Service available on port `5000` **inside the cluster** and told it to send that traffic to port `5000` on the matching Pods. Not from the outside of the Cluster.
The clients' browser is outside that cluster.

So when we visit:

```text
http://localhost:5000
```

our browser is trying to connect to port `5000` on **our computer**, not to the Service inside Kubernetes.

For local development, Kubernetes gives us a convenient way to temporarily access resources inside the cluster called: `port-forward`.

`port-forward` connects a port on **our computer** to a resource **inside the Kubernetes cluster**.

Let's use it to access our Service:

```bash
kubectl port-forward service/app-one 5000:5000
```

![[kubernetes-port-forwarding-5000-app-one-results-terminal.png|411]]

Now open:

```text
http://localhost:5000
```

Our application should finally be reachable from the browser.

> [!TIP]- Check if port 5000 is already in use
> You can check which process is listening on a port using:
>
> **macOS**
> ```bash
> lsof -i :5000
> ```
>
> **Linux**
> ```bash
> ss -ltnp | grep :5000
> ```
>
> **Windows (PowerShell)**
> ```powershell
> Get-NetTCPConnection -LocalPort 5000
> ```
>
> If nothing is returned, the port is most likely available.

> [!NOTE]
> `port-forward` is mainly useful for **local development, testing, and debugging**.
>
> The connection only exists while the command is running. In a real environment, applications are typically exposed using other networking solutions, which we'll explore in the next module.

---

# Summary

We started with a container image and used Kubernetes to turn it into a running application.

A **Deployment** described how our application should run and how many replicas we wanted. Kubernetes then used a **ReplicaSet** to maintain that number of **Pods**, with each Pod running our container.

![[kubernetes-service-whole-relationship-diagram.png]]

Because Pods can be created and replaced, we added a **Service** to give them a stable network endpoint.

The Service uses **labels** to find the correct Pods and forwards traffic to the configured `targetPort`.

```text
Service :5000
      │
      ▼
Matching Pods :5000
```

Finally, we discovered that our Service exists **inside the Kubernetes cluster**. To access it from our own computer during development, we used:

```bash
kubectl port-forward service/app-one 5000:5000
```

This temporarily connected `localhost:5000` on our computer to the Service inside Kubernetes.

---

# What's Next?

So far, we've built and deployed **app-one** step by step.
Now it's your turn.

In the next hands-on exercise, you'll take **app-two** and repeat the process yourself:
- Create its **Deployment**
- Run multiple **Pods**
- Create a **Service**
- Access the application from your computer

Continue on: [[Kubernetes - Hands-On Deployment & Service Practice 04]]