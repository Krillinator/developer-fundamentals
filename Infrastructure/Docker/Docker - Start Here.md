---
icon: LiGoal
---
# Overview

![[docker-banner.avif]]

Docker makes developers happy because it packages an application together with everything it needs to run, making the classic **"it works on my machine"** problem much less painful.

What about the little whale? 
It carries **containers**, just like a cargo ship carries shipping containers. Your applications are packaged into standardized containers that can be moved and run in different environments.
# Prerequisites

Before continuing:

- Install Docker Desktop [Docker - Installation & Docker Desktop](<Infrastructure/Docker/module-01/Docker - Installation & Docker Desktop.md>)
	- Make sure Docker Desktop is running
	- Open a terminal

---

# Run Your First Container

Let's start by running something before learning the theory. 
You can skip this step if you've already done this in [[Infrastructure/Docker/module-01/Docker - Installation & Docker Desktop]]

Run:

```bash
$ docker run hello-world
```

You should eventually see:

```text
Hello from Docker!
```

The program runs and then immediately finishes.

> [!success] It Works
> Docker successfully downloaded and ran `hello-world`.

*But what exactly happened?*

---

# What Did Docker Download?

Run:

```bash
$ docker images
```

You should see something similar to:

```text
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    ............   ............   ....
```

Docker downloaded the `hello-world` **image** which is built from a **Docker File**.

![[docker-image-container-flow.jpg]]

That's right, this **image is actually a packaged application!**

It can contain the application code, runtime, libraries, dependencies and other files needed to run it.

And we just **downloaded and ran it on our computer within seconds!** 

> [!tip] "But it works on my computer!"  
> Ever had an application work perfectly on one computer but fail on another because something is missing or configured differently?
> 
> Docker helps solve this by packaging the application and its environment together, so it can run much more consistently across different environments.

---

# Try Removing It

So far, this seems pretty simple: we downloaded an **image** and ran the application.
*But what actually happened behind the scenes?*

Let's try to **remove the image** and see what Docker tells us. We'll quickly discover that running an image involves a little more than we might expect.

> [!tip] Let's Experiment  
> Don't worry about containers or Docker's lifecycle just yet.
> 
> **Let's break something first and learn from what happens.** 

Open the terminal and run:

```bash
$ docker rmi hello-world
```

You may expect Docker to simply delete it.

Instead, you'll probably get an error similar to:

```text
Error response from daemon:
conflict: unable to remove repository reference "hello-world"
container ... is using its referenced image
```

> [!question] Why?
> `hello-world` already finished running.
>
> So why is Docker saying that a **container** is still using the image?
> 
> Well, think about it: 
> > You can't remove the engine from a car when it's running. The car depends on it.
> 
> We don't really know what this **container** is yet, but apparently Docker creates one when we ran the image.


---

# Find the Container

Run:

```bash
$ docker ps
```

> [!NOTE] 
> `ps` stands for **Process Status** and comes from the Unix/Linux `ps` command, which displays running processes.
>
> In Docker, `docker ps` displays your **currently running containers**.
>
> ```bash
> $ docker ps
> ```
>
> You may also see the newer equivalent:
>
> ```bash
> $ docker container ls
> ```
>
> Both commands show the currently running containers.

You may see:

```text
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS
```

"Nothing, it's empty?!"

That's because:

```bash
$ docker ps
```

only shows **running containers**.

Now run:

```bash
$ docker ps -a
```

The `-a` means:

```text
--all
```

Now you should see the `hello-world` container:

```text
CONTAINER ID   IMAGE         STATUS
22891e3b0722   hello-world   Exited (...)
```

> [!important] Exited ≠ Removed
> The `hello-world` program finished running, so the container **stopped**.
>
> But the container still exists.

---

# Remove the Container

Copy the container's **ID** or **name** from:

```bash
$ docker ps -a
```

Then remove it:

```bash
$ docker rm <container>
```

Example:

```bash
$ docker rm 22891e3b0722
```

Check again:

```bash
$ docker ps -a
```

The container should now be gone.

---

# Remove the Image

Now try again:

```bash
$ docker rmi hello-world
```

This time Docker should allow the image to be removed.

Check:

```bash
$ docker images
```

`hello-world` should be gone.

> [!success] Clean Again
> You have now removed both the **container** and the **image**.

---

# So What Just Happened?

Without realizing it, you just worked with two of Docker's most important concepts:

1. **Image**
2. **Container**

When you ran:

```bash
$ docker run hello-world
```

Docker needed the `hello-world` **image**.

If the image wasn't already on your computer, Docker downloaded it.

You can also verify this by opening up docker desktop 

![[docker-run-hello-world-images.png]]

Docker then used that image to create a **container**.

![[docker-run-hello-world-containers.png]]

The important part is:

> The container still exists after it exits.

That's why Docker wouldn't let us remove the image.

---

# Image vs Container

Let's address the elephant - or whale - in the room. We've been throwing around two words: **Image** and **container**. But what are they really?

## Image

For now, we're going to keep this extremely simple. 
Is an **image** just an application?
**Yes.. and no**

There's more to an image, and we'll explore that in the next section.

But for now, this mental model is enough:
	Image = a packaged application

Our `hello-world` image contained everything needed to run the little application. That's the absolute minimum we need to know right now.

---

## Then What is a Container

Imagine we're building an e-commerce application. We might have:
* E-Commerce Website
* Database

We probably don't want to bundle the website and database together. 
Instead, we can separate them:
* Website Image
* Database Image

When we want to actually run them, Docker creates containers from those images.

**Docker doesn't run the image directly**.
Instead, Docker takes the image and creates a container for it. Think of the container as the application's own little environment

![[docker-containers-overview.png]]

What's fascinating about the container is everything Docker can add **around the image**.

A container has its own **configuration, metadata, environment variables, networking and writable filesystem.**

This means docker can take the **same image** and run it in different ways without changing the image itself.

---

## Why Is This Useful?

Now things get interesting.

Our e-commerce application might depend on a database.
Instead of installing and configuring everything directly on our computer, we can run each part in its **own container**.

Each container gets its own environment and configuration, while still being able to communicate with other containers.

![[docker-containers-communicating-overview.png|348]]

Docker can then help us start, configure and connect these environments without having to manually configure everything on the computer itself. And because the **image stays the same**, another developer can use the same images to create the same environments on their computer. 

There's another major benefit to containers: **isolation**. 

Remember that each container gets its **own environment**. That means our website and database don't need to dump all of their dependencies, configuration and processes into the same environment.

> [!success] Remember  
> **Image = the packaged application**  
>  
> **Container = the environment created from that image where it can run**  
>  
> **The image defines what it is. The container defines how it runs.**

[Docker - What is an Image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

[Docker - What is a Container?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/)

---

# Commands We Just Learned

| Command | What it does |
| --- | --- |
| `docker run <image>` | Creates and runs a container from an image |
| `docker images` | Shows downloaded images |
| `docker ps` | Shows running containers |
| `docker ps -a` | Shows all containers |
| `docker rm <container>` | Removes a container |
| `docker rmi <image>` | Removes an image |

> [!note]
> Don't worry about memorizing every Docker command yet.
>
> The important thing is understanding **why** we used them.

---

# What's Next?

We now understand the first important Docker relationship:

```text
Image <--> Container
```

Next, we'll find out where images come from and how Docker knows what should be inside one.

Continue with:
1. [[Infrastructure/Docker/module-01/Docker - Dockerfile]]

Module 01: consists of the following additional steps:
1. [[Infrastructure/Docker/module-01/Docker - Basic Dockerfile Instructions]]
2. [[Infrastructure/Docker/module-01/Docker - Docker Hub]]
3. [[Infrastructure/Docker/module-01/containers/Docker - Container Ports]]
4. [[Infrastructure/Docker/module-01/containers/Docker - Understanding Containers]]
5. [[Infrastructure/Docker/module-01/containers/Docker - Hands-On Container Practice 01]]
6. [[Infrastructure/Docker/module-01/containers/Docker - Hands-On Container Practice 02]]
7. [[Infrastructure/Docker/module-01/containers/Docker - Hands-On Container Practice 03]]
8. [[Infrastructure/Docker/module-01/containers/Docker - Knowledge Check 01]]

Module 02: 
1. [[Infrastructure/Docker/module-02/Docker - Module 02]]
2. [[Infrastructure/Docker/module-02/Docker - Push to Central Registry]]
3. [[Infrastructure/Docker/module-02/Docker - Deploying App Changes]]
4. [[Infrastructure/Docker/module-02/Docker - Understanding Image Layers (theory)]]
5. [[Infrastructure/Docker/module-02/Docker - Hands-On Container Practice 04]]
6. [[Infrastructure/Docker/module-02/Docker - Hands-On Container Practice 05]]
7. [[Infrastructure/Docker/module-02/Docker - Hands-On Container Practice 06]]
8. [[Infrastructure/Docker/module-02/Docker - Knowledge Check 02]]

Module 03: Docker Swarm
1. [[Docker - Module 03]]
2. [[Docker - Preparing Docker Swarm Hosts]]
3. [[Docker - Understanding Docker Swarm (theory)]]
4. [[Docker - Creating The First Swarm]]
5. [[Docker - Deploy Your First Swarm Service]]
6. [[Docker - Scaling a Swarm Service]]
7. [[Docker - Swarm Failure and Recovery]]
