---
icon: LiGitBranchPlus
---
# Overview

![[cloud-banner.jpg]]

Our application is now built as a Docker image and stored on Docker Hub.
But applications don't stay the same forever. We fix bugs, change features and update our code.

> **What happens when the application inside our Docker image changes?**

In this module, we'll make a small change to our Python application, rebuild the image and push the updated version to Docker Hub.

While doing this, we'll discover that Docker doesn't necessarily start from scratch every time we rebuild an image.

---

# Update the Application

Open `app.py`.

Previously, our application returned:

```python
return "hello world!"
```

Change it to:

```python
return "Hello Beautiful World!"
```

Your complete `app.py` should now look like:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello Beautiful World!"

if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

We've changed the application on our computer.
However, the Docker image we built earlier still contains the **previous version** of `app.py`.

> **Changing our source code does not automatically update an image we've already built.**

To get our new application into the image, we need to build it again.

---

# Rebuild the Image

This time, we'll build the image using the Docker Hub name we created in the previous module:

```bash
docker image build -t <username>/docker-python-app .
```

Replace `<username>` with your Docker Hub username.

Watch the build output carefully.
You'll probably notice something interesting.
Some steps may show:

![[docker-rebuild-tag-cached-results-terminal.png|513]]

while the step involving `app.py` needs to run again.
Docker didn't rebuild everything.

---

# Docker Build Cache

Let's look at our Dockerfile again:

```dockerfile
FROM python:3.6.1-alpine

WORKDIR /app

RUN pip install --upgrade pip
RUN pip install flask

COPY app.py .

CMD ["python", "app.py"]
```

We changed:

```text
app.py
```

But we didn't change our base image, working directory or Flask installation.

Docker has already performed those earlier build steps. When Docker determines that a previous result can still be reused, it retrieves that result from its **build cache** instead of doing the same work again.

Thanks to Docker build cache, some steps aren't repeated

![[docker-rebuild-tag-cached-results-explained-visuals.png]]

This saves both time and resources!

> [!NOTE] What is the Docker build cache?
> Docker can reuse results from previous builds when the inputs to those build steps haven't changed.
>
> This avoids repeating work unnecessarily and can make future builds much faster.

> [!NOTE]- Why isn't FROM cached?
> `FROM` is a little different from the other steps. Docker still needs to **resolve and check the base image** that our build starts from.
>
> In our output, Docker checks the metadata for:
>
> ```text
> python:3.6.1-alpine
> ```
>
> It doesn't rebuild or download the base image again if it already has what it needs, but Docker still needs to confirm **which base image the build should use**. That's why we don't cross out `FROM` like the cached `WORKDIR` and `RUN` steps.

---
# Theory: Image Layers

We just saw Docker reuse several parts of our previous build.
How is Docker able to do that?

The answer starts with **image layers**.

> **A layer represents a set of changes made to the image's filesystem.**

Imagine Docker is building our image and reaches:

```dockerfile
RUN pip install flask
```

Installing Flask adds files to the image's filesystem. Docker can store those filesystem changes as a **layer**.

Later, Docker reaches:

```dockerfile
COPY app.py .
```

This adds our `app.py` file to the image's filesystem, creating another set of filesystem changes.

So rather than thinking of our image as one giant block, we can think of its filesystem as being assembled from layers of changes.

```text
Base image: Python + Alpine
Layer: Flask is installed
Layer: app.py is added
```

This connects directly to what we saw during our rebuild.

We changed `app.py`, but we didn't change how Flask was installed. Docker could therefore reuse the earlier cached build results and process the changed `COPY` step again.

> [!NOTE] Does every Dockerfile instruction create a filesystem layer?
> No. A filesystem layer represents **changes to files inside the image**.
>
> Instructions such as `RUN` and `COPY` can make filesystem changes.
>
> `CMD`, on the other hand, doesn't add files to the image. It stores configuration telling Docker **what command should run when a container starts**.
---

---
# Theory: Why Dockerfile Order Matters

So far, Docker's build cache has simply made our second build faster. But there's an important detail we haven't explored yet:

> **The order of our Dockerfile instructions affects how much of that cache Docker can reuse.**

When Docker can no longer reuse the cached result of a build step, the steps that come **after it may also need to be processed again**.

Sounds bad... right?

This means that two Dockerfiles can produce the same application, but one may be much faster to rebuild simply because its instructions are arranged more efficiently.
Let's see what that looks like.

### Example: A Bad Order

Let's imagine we had written our Dockerfile differently:

```dockerfile
FROM python:3.6.1-alpine

WORKDIR /app

COPY app.py .

RUN pip install --upgrade pip
RUN pip install flask

CMD ["python", "app.py"]
```

Now imagine we make one tiny change to `app.py`:

```python
return "Hello again!"
```

Docker reaches:

```dockerfile
COPY app.py .
```

and detects that `app.py` has changed.

The cached result for this step contains the **old version** of `app.py`, so Docker can't reuse it. Docker needs to perform the `COPY` again to add our new version.

But there's another problem.

**Each build step starts from the result of the step before it.**

Because `COPY app.py .` now produces a different result, the steps that follow it can no longer reuse the same cached chain:

```dockerfile
RUN pip install --upgrade pip
RUN pip install flask
```

Docker may therefore need to process these steps again, even though we didn't change anything about Flask.

We've ultimately created unnecessary work in the pipeline.

### Example: A Better Order

Instead, we can install our dependencies **before** copying the frequently changing application code:

```dockerfile
FROM python:3.6.1-alpine

WORKDIR /app

RUN pip install --upgrade pip
RUN pip install flask

COPY app.py .

CMD ["python", "app.py"]
```

Now imagine we change `app.py` again.

Docker can still reuse the cached results for:

```dockerfile
WORKDIR /app

RUN pip install --upgrade pip
RUN pip install flask
```

Only when Docker reaches:

```dockerfile
COPY app.py .
```

does it encounter our changed file.

The expensive installation work has already been reused from the cache.

### Dependencies & Order

This doesn't mean we should blindly put everything that changes frequently at the bottom.
First, each step must come **after the things it depends on**.

When the order is flexible, a useful principle is:

> [!TIP] Dockerfile Order
> Put things that **change less frequently earlier** and things that **change more frequently later**.
>
> This gives Docker a better chance to reuse earlier cached work when something changes.

---

# Run the Updated Image

Before publishing our new image, let's make sure it works.

Run:

```bash
docker run -p 5000:5000 -d <username>/docker-python-app
```

> [!NOTE]
> If your previous container is still using port `5000`, stop or remove that container before starting the new one.

Visit:

```text
localhost:5000
```

And there we go:

![[docker-rebuild-web-browser-results-hello-beautiful-world.png|427]]

Our rebuilt image contains the updated application.

---

# Push the Updated Image

Now let's publish the rebuilt image:

```bash
docker push <username>/docker-python-app
```

Watch the output.

This time, you may notice messages such as:

```text
Layer already exists
```

alongside layers that are actually pushed.

Why doesn't Docker upload the entire image again?

Docker Hub already has content from the image we pushed in the previous module. Content that hasn't changed doesn't need to be uploaded again.

Only content that the registry doesn't already have needs to be transferred.

> [!NOTE] Build cache vs registry layers
> During **build**, Docker can reuse cached build results instead of repeating unchanged work.
>
> During **push**, the registry can avoid receiving image layers it already has.
>
> They're related to Docker's layered image model, but they're two different parts of the process.

---

# Check Docker Hub

Open [Docker Hub](https://hub.docker.com/) and navigate to your `docker-python-app` repository.

We've pushed the rebuilt image using the same tag:

```text
<username>/docker-python-app:latest
```

The `latest` tag in Docker Hub now refers to the image we just pushed.

![[docker-rebuild-push-image-docker-hub-results.png]]

Remember that `latest` isn't automatically calculated by Docker to determine which image is newest. It's simply the tag we're choosing to use.

---

# What Did We Learn?

We started with an application that had already been built and published. Then we changed its source code and followed the update through Docker.

We learned that:

- Changing `app.py` does not change an image that has already been built
- We need to rebuild the image to include our changes
- Docker can reuse previous build results through its **build cache**
- Docker images use **layers**
- Changing a build step can affect the steps that follow it
- Dockerfile order can improve cache efficiency
- Docker Hub doesn't need to receive image layers it already has
- We can push a rebuilt image using an existing tag such as `latest`

> **Docker can reuse unchanged work instead of rebuilding and transferring everything from scratch.**

---

# What's Next?

We've now seen Docker image layers in action.

We saw them while rebuilding our application, and we saw Docker Hub recognize layers that were already available when we pushed the updated image.

In the next module, we'll take a closer look at **image layers themselves** and inspect how our Docker image is built.

Continue with: [[Infrastructure/Docker/module-02/Docker - Understanding Image Layers (theory)]]