---
icon: LiTestTubeDiagonal
---
# Overview

![[lab-image.jpg]]

> **What happens to a container when we stop it, and how do we remove it completely?**

So far, we've created and interacted with containers, but there's an important distinction we haven't explored yet: **stopping a container does not remove it**.

We did stop a container before, however, practice makes perfect!

In this lab, we'll create two containers, stop them and then remove them so we can see exactly how a container moves through these different states.

---

# 1. Create Two Containers

Let's start by creating two containers using the **Nginx image**:

```bash
$ docker container run -d --name web-one nginx
$ docker container run -d --name web-two nginx
```

The `--name` flag lets us give each container a recognizable name instead of relying on its randomly generated Container ID.

We're also using a new flag here:

> [!NOTE] What does `-d` mean?
> `-d` stands for **detached** and runs the container in the background.
>
> This means the container keeps running without taking over our terminal. Unlike earlier when we kept `top` running in one terminal and had to open a second terminal to continue working, we can now start multiple containers and keep using the **same terminal** while they continue running in the background.

We should now have two containers running:

```text
web-one
web-two
```

---

# 2. Check the Running Containers

Run:

```bash
$ docker container ls
or
$ docker ps
```

You should see both containers in the list.

```text
CONTAINER ID   IMAGE   STATUS       NAMES
...            nginx   Up ...       web-one
...            nginx   Up ...       web-two
```

At this point, both containers have been **created and are running**.

---

# 3. Stop the Containers

Now let's stop them:

```bash
$ docker container stop web-one web-two
```

Docker should return their names:

```text
web-one
web-two
```

Run this again:

```bash
$ docker container ls
or 
$ docker ps
```

Our containers are gone from the list.
But have we actually deleted them?

No. `docker container ls` only shows **running containers**.
To see stopped containers as well, run:

```bash
$ docker container ls -a
or
$ docker ps -a
```

You should find `web-one` and `web-two` again, but their status will now show that they have **exited**.

> [!NOTE] Stopped does not mean deleted
> Stopping a container only stops its running processes. The container itself still exists and can be started again later.

---

# 4. Clean Up the Containers

Our containers are now stopped, but they still exist.

Since we're specifically cleaning up **containers**, run:

```bash
docker container prune
```

Docker will show what it intends to remove and ask us to confirm before continuing.

`docker container prune` removes **all stopped containers**, so always check what you have before confirming

## What About Images?

Removing our containers does **not** remove the images they were created from.
Check your images:

```bash
$ docker image ls
or 
$ docker images
```

You should still see the `nginx` image we used to create our containers.
Docker has a separate prune command for images:

```bash
$ docker image prune
```

This removes **dangling images** that are no longer referenced.
If we want Docker to remove **all unused images**, we can instead use:

```bash
$ docker image prune -a
```

---

# What Did We Learn?

A container can exist even when it isn't currently running. In this lab, we saw the difference between **stopping** a container and **removing** one.

The important distinction is:

> **`docker container stop` stops the processes inside a container, while `docker container rm` removes the container itself.**

Whenever you're ready, head over to the last hands-on for this module:
 [[Infrastructure/Docker/module-01/containers/Docker - Hands-On Container Practice 03]]