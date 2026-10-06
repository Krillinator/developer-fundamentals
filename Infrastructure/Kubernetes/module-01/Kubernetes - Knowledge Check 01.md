---
icon: ☑
---
# Quiz

![[quiz-test-img.jpg|361]]

Before moving on, test what you've learned so far.

# Quiz

Before moving on, test what you've learned about **Docker images, Dockerfiles and containers**.

---

### 1. What does the `docker run` command do?

- [ ] It runs the Docker daemon.
- [ ] It runs a Dockerfile.
- [ ] It runs a container as an image.
- [ ] It runs an image as a container.

### 2. Consider this Dockerfile:

```dockerfile
FROM ubuntu:18.04

COPY . /app

RUN make /app

CMD python /app/app.py
```

What does the `COPY` instruction do?

- [ ] It copies the contents of the `/app` directory into the working directory of the image as a new layer.
- [ ] It copies all application files into the working directory of the image as a new layer.
- [ ] It copies the contents of the root directory into the `/app` directory of the image as a new layer.
- [ ] It copies the contents of the current directory into the `/app` directory of the image as a new layer.

### 3. It is possible for containers to use features and resources of the host operating system.

- [ ] True
- [ ] False

### 4. Which command names an image `my-app` and tags it `v1`?

- [ ] `docker build -t my-app:v1 .`
- [ ] `docker tag -n my-app -t v1 .`
- [ ] `docker copy -v my-app:v1 .`
- [ ] `docker build -n my-app v1 .`

### 5. You can use the Docker `COPY` instruction to copy files from your local machine or from remote URLs.

- [ ] True
- [ ] False

### 6. Which of the following is a benefit of containers?

- [ ] Each container is fully isolated and therefore secure.
- [ ] Containers provide a standardized way to package and ship software.
- [ ] Each container runs its own operating system (OS).
- [ ] Like virtual machines (VMs), containers virtualize your infrastructure.

### 7. What is an image?

- [ ] A text file that contains the commands and settings that will run a container and the applications running in that container.
- [ ] A YAML file with key/value pairs specifying the attributes of a container.
- [ ] A read-only file that contains the source code, libraries and dependencies needed to run an application.
- [ ] An isolated process running on a local or remote host with its own filesystem and networking.

### 8. Consider this Dockerfile:

```dockerfile
FROM ubuntu:18.04

COPY . /app

RUN make /app

CMD python /app/app.py
```

What does the `FROM` instruction do?

- [ ] It defines the virtualized host operating system on which the container will run.
- [ ] It defines the minimum version of the operating system for the `docker build` command to use.
- [ ] It defines the operating system on which the `docker build` command must be run.
- [ ] It defines the base image, which in this case is Ubuntu version 18.04.

### 9. What does the `docker build` command do?

- [ ] It uses a Dockerfile to create an image.
- [ ] It uses an image to create a container.
- [ ] It creates a Docker app.
- [ ] It creates a Dockerfile.

### 10. You can use the Docker `COPY` instruction to copy files from your local machine to the Docker image.

- [ ] True
- [ ] False

---

**See how you did below.**

> [!success]- Quiz Answers
> **1. It runs an image as a container.**
>
> `docker run` creates and starts a container from an image.
>
> ```bash
> docker run nginx
> ```
>
> Here, Docker uses the `nginx` image to create and run a container.
>
> **2. It copies the contents of the current directory into the `/app` directory of the image as a new layer.**
>
> ```dockerfile
> COPY . /app
> ```
>
> `.` represents the current build context, while `/app` is the destination inside the image.
>
> **3. True**
>
> Containers can use features and resources provided by the host operating system.
>
> Unlike virtual machines, containers generally share the host's kernel rather than running a complete operating system of their own.
>
> **4. `docker build -t my-app:v1 .`**
>
> The `-t` option assigns a name and optional tag to the image:
>
> ```text
> my-app:v1
> │      │
> │      └── tag
> └───────── image name
> ```
>
> **5. False**
>
> `COPY` can copy files and directories from the Docker build context into the image, but it cannot directly download arbitrary remote URLs.
>
> ```dockerfile
> COPY app.py /app/
> ```
>
> **6. Containers provide a standardized way to package and ship software.**
>
> A container image can package an application together with the files, libraries and dependencies it needs.
>
> Containers provide isolation, but they are not automatically or completely secure.
>
> **7. A read-only file that contains the source code, libraries and dependencies needed to run an application.**
>
> An **image** is the packaged template used to create containers.
>
> A useful way to remember the difference is:
>
> ```text
> Image       = packaged template
> Container   = running instance of an image
> Dockerfile  = instructions for building an image
> ```
>
> **8. It defines the base image, which in this case is Ubuntu version 18.04.**
>
> ```dockerfile
> FROM ubuntu:18.04
> ```
>
> `FROM` specifies the base image that the rest of the Docker image will be built on.
>
> **9. It uses a Dockerfile to create an image.**
>
> For example:
>
> ```bash
> docker build -t my-app .
> ```
>
> Docker reads the Dockerfile and uses its instructions to build an image.
>
> **10. True**
>
> `COPY` can copy local files from the build context into the Docker image.
>
> For example:
>
> ```dockerfile
> COPY . /app
> ```
>
> This copies the contents of the current build context into `/app` inside the image.
>
> ---
>
> **Passing grade: 80%**

**Passing grade: 80%**

Continue on: [[Infrastructure/Kubernetes/module-01/Kubernetes - Hands-On Container Practice 01]]