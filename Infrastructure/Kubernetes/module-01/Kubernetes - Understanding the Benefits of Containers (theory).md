---
icon: LiBook
---
# Overview

![[docker-banner.avif]]

We've already worked with Docker, so the goal here isn't to relearn how to run a container. Instead, let's recap **why containers are useful in the first place**.

---

## The Problem Containers Solve

Applications rarely consist of source code alone. They depend on runtimes, libraries, configuration and specific versions of software.

Without containers, differences between environments can cause the classic:

> "It works on my machine."

Containers solve much of this by packaging the application and its dependencies into a **consistent, portable environment**.


---

## Why Containers?

Containers provide **portability, consistency, isolation and efficiency** by packaging applications into lightweight, repeatable environments that can run consistently across different systems and be quickly created or removed when scaling is needed.


---

## The Basic Workflow

We've already seen the core Docker workflow:

```text
Dockerfile - Build - Image - Container
```

The **Dockerfile** describes how the application should be packaged.
[[Docker - Dockerfile]]

The resulting **image** is the read-only package containing the application and its dependencies.

A **container** is a running instance of that image.

![[docker-containers-overview.png|518]]

---

## Beyond a Single Machine

Running containers locally is only the beginning.

Images can be stored in a **container registry**, allowing other machines and infrastructure to retrieve and run them.

This portability and consistency become especially useful when we move beyond a single machine.

> [!summary]
> Containers give us a **portable, consistent and lightweight way to package and run applications**.
>
> Docker gives us the tools to build and run them.
>
> The next challenge is managing containers across infrastructure, which is where **container orchestration and Kubernetes** come in.


---

## RECAP: Building an Image

Docker uses the `docker build` command to create an image from a Dockerfile.

```bash
docker build -t my-app:v1 .
```

The `-t` option lets us assign a **name and tag** to the image.


---

## Dockerfile Instructions

Consider the following Dockerfile:

```dockerfile
FROM ubuntu:18.04

COPY . /app

RUN make /app

CMD python /app/app.py
```

Each instruction has a different purpose:

| Instruction | Purpose |
|---|---|
| `FROM` | Selects the base image |
| `COPY` | Copies files into the image |
| `RUN` | Executes a command while building the image |
| `CMD` | Defines the default command when a container starts |

### `FROM`

```dockerfile
FROM ubuntu:18.04
```

`FROM` defines the **base image** that we build on top of.

In this case, the base image is Ubuntu 18.04.

### `COPY`

```dockerfile
COPY . /app
```

The syntax is:

```text
COPY <source> <destination>
```

So here:

```text
.       = current build context
/app    = destination inside the image
```

Docker copies the contents of the current build context into `/app` in the image.

> [!important]
> `COPY` can copy files and directories available to the build context, but it does **not** directly download arbitrary files from remote URLs.


---

## Running an Image

Once the image exists, we can create and start a container from it:

```bash
docker run my-app:v1
```

The image remains the reusable, read-only package while the container becomes the **running instance**.

---

## Containers and the Host

Containers are isolated, but they do not normally run a complete operating system of their own.

Instead, containers can use features and resources provided by the **host operating system**, including its kernel.

![[containers-and-kernel.png|374]]

This is one reason containers can be considerably more lightweight than virtual machines.

Containers isolate applications from each other, but this isolation is **not an automatic security boundary**.  
  
Containers still share the host's kernel, and their security depends on how they are configured and what permissions they are given.  
  
For example, a container can become less secure if it:  
- Runs with unnecessary privileges.  
- Has access to sensitive host files or directories.  
- Uses vulnerable or outdated images.  
- Exposes unnecessary network ports.  
- Runs applications as `root` when it isn't necessary.  
  
> [!important]  
> **Isolation ≠ automatically secure**  
>  
> Containers provide isolation, but security still depends on the image, application, permissions, configuration and host system.


---

## Summary

| Concept | Remember |
|---|---|
| **Dockerfile** | Instructions for building an image |
| **Image** | Read-only application package |
| **Container** | Running instance of an image |
| `docker build` | Builds an image from a Dockerfile |
| `docker run` | Creates and runs a container from an image |
| `FROM` | Defines the base image |
| `COPY` | Copies build-context files into the image |
| `-t` | Names and tags an image |

> [!summary]
> Images give us a standardized way to package and distribute software, while containers give us isolated running instances of those images.

---

## What's Next?

Next, we'll revisit the **core Docker workflow** and connect the pieces we've already worked with.

We'll look at how a **Dockerfile becomes an image**, how instructions such as `FROM` and `COPY` affect that image, how images are **named and tagged**, and how `docker run` turns an image into a running container.

We'll also clarify how containers interact with the **host operating system** and what container isolation actually means.

Continue on: [[Kubernetes - Docker Images, Dockerfiles and Containers]]