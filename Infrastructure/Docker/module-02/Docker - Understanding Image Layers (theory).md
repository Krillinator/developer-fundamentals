---
icon: LiLayers
---

## Overview

![[layers-img.png|243]]

In the previous section, we discovered something interesting.
When we changed only `app.py`, Docker was able to reuse most of our previous build:

```text
CACHED [2/5] WORKDIR /app
CACHED [3/5] RUN pip install --upgrade pip
CACHED [4/5] RUN pip install flask
       [5/5] COPY app.py .
```

Docker didn't rebuild everything from scratch which raises a question:

> **What exactly is Docker reusing?**

To understand that, we need to look more closely at how a Docker image is constructed.

---

# An Image Is Built From Layers

![[docker-image-is-not-one-big-block-diagram.png|530]]

**A Docker image is not stored as one large, monolithic block of filesystem data.**

Instead, its filesystem is built from a series of **filesystem changes**, which Docker stores in something called: **layers.**

The distinction is:

> **The instruction is the action/request.**
> **The layer is the filesystem result produced by that action.**

Consider part of our Dockerfile:

```dockerfile
RUN pip install --upgrade pip
RUN pip install flask
COPY app.py .
```

Docker processes these instructions **from top to bottom**.


### From Instruction to Layer

Take this instruction:

```dockerfile
RUN pip install flask
```

The instruction itself is **not the layer**.

It tells Docker to run `pip install flask`.
Running that command changes the filesystem by adding Flask, its dependencies and other related files.

Docker stores those resulting filesystem changes in a **filesystem layer**.

> [!IMPORTANT]
> A Dockerfile instruction and a filesystem layer are **not the same thing**.
>
> The instruction tells Docker what to do.  
> The layer stores the resulting filesystem changes.


### Layers Build Upon Each Other

Every image needs a starting point.
In our Dockerfile, we choose that starting point with:

```dockerfile
FROM python:3.6.1-alpine
```

This tells Docker:

> **Use `python:3.6.1-alpine` as the foundation for the image I'm about to build.**

and `python:3.6.1-alpine` is already a Docker image. It was built before ours, and it already has its own filesystem layers.

Those existing layers become the **bottom of our image**.

Docker then continues through the rest of our Dockerfile. When an instruction produces filesystem changes, Docker stores those changes in layers above the existing ones.

This means the **base image layers are at the bottom**, while filesystem changes produced later in the build appear higher in the stack.

> [!IMPORTANT]
> **Each layer contains filesystem changes relative to the filesystem represented by the layers beneath it.**

![[docker-image-layers-diagram.png]]

The blocks in the diagram represent the **resulting filesystem layers**, not the Dockerfile instructions themselves.

The instructions shown inside the blocks tell us **what caused those filesystem changes**.

For example, `COPY app.py .` is the instruction. 
Docker performs that instruction, `/app/app.py` is added to the filesystem, and that resulting change is stored in the filesystem layer shown at the top.

So remember:

> **The instruction is the action/request.**
> **The layer is the filesystem result produced by that action.**

---

## One Filesystem

A Docker image is **stored as separate, read-only layers**, not as one large block.

When a container uses the image, Docker stacks those layers together so they appear as **one filesystem**.
The layers remain separate underneath, but the container doesn't need to care about that.

> [!SUMMARY]
> **Docker stores the image in layers. The container sees those layers as one filesystem.**
---


---

## Layers Can Be Shared

Layers also make it possible for different Docker images to reuse the same underlying filesystem data.

Imagine we build two similar applications:

```text
Image 1                     Image 2

app.py                      app2.py
Flask                       Flask
Python + Alpine             Python + Alpine
```

Both applications use the same Python base image and install the same dependencies.

Instead of requiring completely separate copies of all that identical filesystem data, the images can reference the same underlying layers.

Only the filesystem content that differs needs to be unique.

![[docker-image-layers-caching-diagram.png]]

In this example, both images can share the filesystem layers containing **Python, Alpine and Flask**.

Their application files are different:

```text
app.py
app2.py
```

so the filesystem changes produced by their respective `COPY` instructions are unique to each image.

> [!NOTE]
> The diagram above shows `CMD` as if it were another filesystem layer.
>
> This is a simplification in the original diagram.
>
> `CMD` stores **image configuration** rather than filesystem changes, so it should not be thought of as a filesystem layer like `RUN` or `COPY`.

This ability to reuse layers is one reason Docker images can be stored and transferred efficiently.
If Docker already has a particular layer, it may be able to reuse that existing layer rather than storing or transferring the same filesystem content again.

---

# Why This Explains Our Cache

Now we can connect layers back to what we observed earlier.
We changed:

```text
app.py
```

but we didn't change how Flask was installed.
Docker could reuse the earlier unchanged build results and only recreate the part affected by our changed application.
That's one of the reasons layers are so useful:

> Docker doesn't necessarily need to rebuild an entire image when only part of it has changed.

---

## Layers Can Be Shared

There's another benefit to the layer structure we saw earlier: **layers can be shared between images.**

If two images need identical filesystem data, Docker can reuse the same existing layers instead of storing another identical copy.

For example, two different Python applications might use the same Python base image and the same Flask dependencies. Their application code is different, but the underlying layers can be shared.

Only the layers containing different filesystem changes need to be unique to each image.

> [!SUMMARY]
> **Different images can share the same layers when their filesystem data is identical.**

---

# Image Layers Are Read-Only

So far, we've seen that Docker images are built from layers and that those layers can even be shared between images.

There's an important reason this works:

> **Once an image has been built, its filesystem layers are read-only.**

Docker doesn't change those layers when a container uses the image. This means the same image layers can safely be reused by multiple containers without one container changing the image for everyone else.

But running applications often need to change their filesystem. They might create files, modify existing files, delete files or write temporary data.

If the image layers are read-only, **where do those changes go?**

---

# The Container's Writable Layer

When Docker creates a container from an image, it adds a **writable container layer** on top of the image's read-only layers.

The image layers remain unchanged, while the container stores its own filesystem changes in the writable layer.

This gives us an important distinction:

> **Image = read-only layers**
> **Container = image layers + its own writable layer**

So if a running container creates a new file, that file isn't added back into the image. It belongs to that particular container.

This is also why multiple containers can use the same image without changing each other's filesystem changes. They can share the read-only image layers while having their own writable layers.

---

# Copy-on-Write

**Imagine this:**
Our image contains a configuration file:

```text
/app/config.txt
```

with a setting:

```text
theme=light
```

When we create a container from the image, the application can **read** `config.txt` and see `theme=light`.

But the file itself belongs to a **read-only image layer**. The container can use the file, but it cannot change the original copy stored in the image.

![[docker-copy-on-write-diagram-step-1.png|429]]

Now imagine our application allows a user to change the theme from:

```text
theme=light
```

to:

```text
theme=dark
```

The application now needs to **modify `config.txt`**.

But if the original file is in a read-only image layer, **where can Docker store the changed version?**

This is where **copy-on-write** comes in.

Docker copies `config.txt` into the container's writable layer and applies the change there. The original file in the image remains untouched.

![[docker-copy-on-write-diagram-step-2.png|348]]

There are now two versions underneath: the original `config.txt` in the read-only image layer and the modified `config.txt` in the container's writable layer.

When the application accesses `/app/config.txt`, the container sees the **modified version from its writable layer**.

![[docker-copy-on-write-diagram-step-3.png|348]]

The original image hasn't changed. If we create another container from the same image, it still starts with the original `config.txt`.

> [!SUMMARY]
> **Copy-on-write lets a container modify a file from the image without changing the image itself.**
>
> Docker keeps the original file in the read-only image layer and stores the container's modified version in its writable layer.
>
> The application doesn't need special Docker commands to do this. It reads and writes files normally through its code, while Docker handles copy-on-write underneath.


---
# Multiple Containers From One Image

Now imagine starting three containers from the same image.
They can all use the same read-only image layers.
But each container receives its **own writable layer**.

Conceptually:

```text
Container A - its own writable changes
Container B - its own writable changes
Container C - its own writable changes

All three - shared read-only image layers
```

That's one reason creating additional containers from an existing image can be lightweight.
Docker doesn't need to duplicate the entire image filesystem for every container.

---

# Inspecting Image History

We've talked about layers conceptually.
Now let's inspect our actual image.

Run:

```bash
$ docker image history <username>/docker-python-app
```

Docker will display the history that contributed to the image.

![[docker-image-history-terminal.png]]

Notice the entries that we never wrote?
Where did those come from?

Remember the first line of our Dockerfile:

```dockerfile
FROM python:3.6.1-alpine
```

Our image starts from an already **existing image**.
That base image already has its own filesystem and build history.
We then add our own changes on top of it.

> [!NOTE]
> `docker image history` shows the history that contributed to an image. Some entries may represent image configuration rather than filesystem changes, so don't interpret every line as a separate filesystem layer.

---

# Layers & Docker Hub

Layers also explain something we may see when pushing an updated image:

![[docker-image-history-layer-already-exists-terminal.png|311]]

Imagine we've already pushed our image to Docker Hub.
Docker Hub now has the layers that make up that image.

Later, we change only:

```text
app.py
```

and rebuild the image.

The Python base and Flask dependencies haven't changed. Only our application code has changed.

When we push the new image, Docker Hub may recognize that it **already has the unchanged layers** from our previous push.

Those layers don't need to be uploaded again.
That's what a message like this is telling us:

```text
Layer already exists
```

Docker can reuse the layer that's already stored in the registry instead of uploading another identical copy.

> [!IMPORTANT]
> This is different from the **build cache** we learned about earlier.
>
> **Build cache** avoids repeating unchanged build work on our machine.
>
> **Registry layer reuse** avoids uploading layer content that the registry already has.

Both are possible because Docker images are made from reusable layers instead of being stored as one giant block.

---

# What Did We Learn?

A Docker image's filesystem is assembled from **read-only layers**.

Instructions such as `RUN` and `COPY` can produce filesystem changes that are stored in layers.

Those layers can be reused between builds, images and containers.

When a container starts, Docker adds a **writable container layer** on top of the image.

If the container modifies something from the image, Docker keeps that change in the container's writable layer rather than modifying the original image.

This is the basic idea behind **copy-on-write**.

Most importantly:

> **Image layers provide the reusable foundation. The container adds its own writable changes on top.**


---

# Clean Up

We've created, rebuilt and run several versions of our application throughout this module.
Before we finish, let's clean up the Docker resources we no longer need.

Using what you learned earlier:
- Remove the containers created during this module.
- Remove the local images we created for `docker-python-app`.
- Verify that they have been removed.

> [!QUESTION]
> If we've already pushed our image to Docker Hub, what happens to it when we delete the image from our computer?

> [!SUCCESS]- Answer
> **Nothing happens to the image stored on Docker Hub.**
>
> Removing an image locally only affects the image on our machine. The image we pushed to Docker Hub remains in the registry and can be pulled again later.
>
> You may discover that Docker refuses to remove an image by its **image ID**, saying that the image is referenced in multiple repositories.
>
> In that case, try removing the image using its **name and tag** instead:
>
> ```bash
> docker image rm docker-python-app:latest
> ```
>
> Docker may respond with:
>
> ```text
> Untagged: docker-python-app:latest
> ```
>
> **So what does "untagged" mean?**
>
> A tag such as `docker-python-app:latest` is a **name that points to an image**. Untagging removes that name/reference from the image.
>
> It does not necessarily mean that Docker immediately deletes all of the underlying image data. That data may still be needed or referenced elsewhere.
>
> Use:
>
> ```bash
> docker image ls
> ```
>
> to verify that the image is no longer listed locally.
>
> **Remember:** none of this affects the image stored on Docker Hub. Local images and images stored in a registry are separate.

---

# What's Next?

We've now explored how Docker images work beneath the surface, from **filesystem layers and caching** to **copy-on-write and registry layer reuse**.

Next, it's time to put everything we've learned into practice.

The next three modules will be hands-on exercises where you'll work through the Docker workflow yourself:

1. **Build an Application From Scratch**  
   Create a small Python web application, write its Dockerfile from scratch, build the image and run it as a container. 

2. **Publish an Image to Docker Hub**  
   Prepare the image for Docker Hub and publish it to a container registry.

3. **Update and Redeploy the Application**  
   Change the application, rebuild and publish the updated image, remove the local containers and images, then pull the latest version from Docker Hub and run it again.
   
4. Theory Test


When you're ready: [[Infrastructure/Docker/module-02/Docker - Hands-On Container Practice 04]]
