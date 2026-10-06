---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

We've covered the core Docker workflow: **Dockerfiles, images, containers and registries**.
Now it's time to put those pieces together without a guided Docker walkthrough.

You'll be given a small Python application. The Python side is already taken care of.

Your job is to decide how to **containerize, build and run the application**, then publish the finished image to **Docker Hub**.

> **The goal is to take an application from source code to a published container image using what you've learned so far.**

---

# 1. Prepare the Application

Create a new project:

```bash
mkdir docker-python
cd docker-python
```

Create the Python file.

Add the following code:

```python
print("Hello from Docker!")
```

Save the file.

You can verify that the application works with:

```bash
python3 app.py
```

You should see:

```text
Hello from Docker!
```

That's all the Python you'll need.

Your project currently looks like:

```text
docker-python/
 app.py
```

From here, the Docker work is yours.

---

# 2. Your Task

Starting with only `app.py`, complete the Docker workflow we've covered so far.

When you're finished, you should have:
- Created a Dockerfile for the application.
- Built a Docker image.
- Named and tagged the image.
- Verified that the image exists locally.
- Created and ran a container from the image.
- Verified that the application runs successfully inside the container.
- Inspected the container after it has finished running.
- Published the image to Docker Hub.

Use the following name and tag for your local image:

```text
python-hello:v1
```

The application should produce:

```text
Hello from Docker!
```

Try to complete the Docker portion without looking at the solution.

---

# 3. Publish to Docker Hub

Once your image works locally, publish it to your Docker Hub account.

Log in if necessary:

```bash
docker login
```

Docker Hub images use the naming structure:

```text
<username>/<repository>:<tag>
```

Tag your finished image for your Docker Hub account:

```bash
docker tag python-hello:v1 <username>/python-hello:v1
```

Then push it:

```bash
docker push <username>/python-hello:v1
```

Replace `<username>` with your Docker Hub username.

After the push completes, verify that the image appears in your Docker Hub repository.

---

# Expected Result

By the end of the lab, you should have completed the entire workflow:

```text
app.py - Dockerfile - Image - Container - Docker Hub
```

Your application should run successfully from the image, and the finished image should exist both **locally** and in a **container registry**.

---

> [!success]- Solution
> Your finished project should contain:
>
> ```text
> docker-python/
> ├── app.py
> └── Dockerfile
> ```
>
> A possible Dockerfile is:
>
> ```dockerfile
> FROM python:3.13-slim
>
> WORKDIR /app
>
> COPY app.py .
>
> CMD ["python", "app.py"]
> ```
>
> Build and tag the image:
>
> ```bash
> docker build -t python-hello:v1 .
> ```
>
> Verify the image:
>
> ```bash
> docker image ls
> ```
>
> Run a container:
>
> ```bash
> docker run python-hello:v1
> ```
>
> You should see:
>
> ```text
> Hello from Docker!
> ```
>
> Inspect the container:
>
> ```bash
> docker container ls -a
> ```
>
> Tag the image for Docker Hub:
>
> ```bash
> docker tag python-hello:v1 <username>/python-hello:v1
> ```
>
> Push it:
>
> ```bash
> docker push <username>/python-hello:v1
> ```
>
> The complete workflow was:
>
> ```text
> app.py
>    │
>    ▼
> Dockerfile
>    │
>    │ docker build
>    ▼
> Image
>    │
>    │ docker run
>    ▼
> Container
>    │
>    │ docker push
>    ▼
> Docker Hub
> ```

---

# What Did We Practice?

This lab brought together the complete Docker workflow we've covered so far.

The application itself was deliberately simple. The challenge was understanding how Docker takes that application, packages it into an image, creates containers from that image and makes the image available beyond your local machine.

This gives us the foundation we need to move from **running individual containers** toward infrastructure capable of managing them.

Continue on: [[Kubernetes - Module 02]]