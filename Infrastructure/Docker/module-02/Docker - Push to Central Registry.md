---
icon: LiUpload
---
# Overview

![[cloud-banner.jpg]]

In the previous section, we built our own Docker image.
We can use that image to create containers on our computer, but right now the image is **only stored locally**.

> **What if another developer wanted to run the same image on their computer?**

We need somewhere both of us can access it.

This is where a **container registry** comes in. A container registry is a central place where Docker images can be stored and distributed.

For this module, we're going to use **Docker Hub**.

---

# Create a Docker Hub Account

Navigate to [Docker Hub](https://hub.docker.com/) and create a free account if you don't already have one.
Docker Hub is a **container registry** for storing and distributing Docker images.

We've actually already been using it.
When we previously ran:

```bash
docker run hello-world
```

Docker downloaded the `hello-world` image for us.

The same thing happened when our Dockerfile used:

```dockerfile
FROM python:3.6.1-alpine
```

Docker needed the Python image before it could build our own image.
Until now, we've been **downloading images**.
This time, we're going to do the opposite and **upload our own**.

>[!note] Is Docker Hub the only registry?  
>Docker images can be stored in many different container registries, including **GitHub Container Registry (GHCR)**, **Amazon ECR**, **Azure Container Registry** and others.
>
>We're using Docker Hub because it keeps our first registry exercise simple. The same general idea of **pushing and pulling images** applies to other registries as well.

---

# Log In to Docker Hub

Before we can push an image to our Docker Hub account, Docker needs to know who we are.

Run:

```bash
docker login
```

Follow the instructions in the terminal to sign in to your Docker Hub account.
Once authenticated, we're ready to publish our image.

---

# Prepare Our Image

Let's first check that our image is still available:

```bash
docker images
```

You should find something similar to:

```text
REPOSITORY          TAG
docker-python-app   latest
```

Our image has a local name, but Docker Hub needs to know **which account the image belongs to**.

Docker Hub image names commonly follow this format:

```text
<username>/<image-name>:<tag>
```

For example:

```text
my-username/docker-python-app:latest
```

Let's give our existing image a name that follows this format:

```bash
docker tag docker-python-app:latest <username>/docker-python-app:latest
```

Replace `<username>` with your Docker Hub username.

> [!NOTE] What does `docker tag` do?
> `docker tag` gives an existing image **another name**.
>
> It does not rebuild the image or create another copy of its contents.
>
> Our image can now be referenced using:
>
> ```text
> <username>/docker-python-app:latest
> ```

Run:

```bash
docker images
```

You should now see both names referencing our image.

---

# Push Our Image

Our image is ready to be uploaded.

Run:

```bash
docker push <username>/docker-python-app:latest
```

Docker will begin sending the image to Docker Hub.

Once the push has completed, our image is no longer available only on our computer. A copy is now stored in our Docker Hub repository as well.

---

# Check Docker Hub

Open [Docker Hub](https://hub.docker.com/) and navigate to your repositories.

You should now find a repository named:

```text
docker-python-app:latest
```

Inside it, you should find the image we just pushed.

We've successfully taken an image that previously existed only on our computer and made it available through a **container registry**.

---

# Pulling an Image

Now imagine another developer wants to use our application.
They don't need our project directory or our Dockerfile just to use the image we've already built.

They can download it from Docker Hub:

```bash
docker pull <username>/docker-python-app
```

Once downloaded, they have the same Docker image available on their computer and can use it to create containers.

This is the same basic process we've already experienced with images such as `hello-world`. The difference is that **we're now the ones publishing the image**.


---

# What's Next?

What happens when we change the application itself?

In the next module, we'll modify `app.py` and rebuild our image. While doing that, we'll notice that Docker doesn't necessarily rebuild or upload everything from scratch.

That will introduce us to two important concepts:

- **Docker image layers**
- **Docker build caching**

Continue with: [[Infrastructure/Docker/module-02/Docker - Deploying App Changes]]