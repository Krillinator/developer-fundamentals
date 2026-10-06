---
icon: ☑
---
# Quiz

![[quiz-test-img.jpg|361]]

Before moving on, test what you've learned about **Docker images, layers, caching and registries**.


---

### 1. How does Docker process the instructions in a Dockerfile?

- [ ] From bottom to top
- [ ] From top to bottom
- [ ] In alphabetical order
- [ ] Docker chooses the order automatically

### 2. What is the relationship between a Dockerfile instruction and an image layer?

- [ ] They are exactly the same thing
- [ ] The instruction tells Docker what to do, while a filesystem layer can store the resulting filesystem changes
- [ ] Every Dockerfile instruction creates a filesystem layer
- [ ] Layers contain the Dockerfile instructions themselves

### 3. Consider this Dockerfile:

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY app.py .

RUN pip install flask

CMD ["python", "app.py"]
```

You frequently change `app.py`, but rarely change your dependencies.

Why could this order result in unnecessary work during rebuilds?

- [ ] `FROM` should always be the final instruction
- [ ] Changing `app.py` affects the `COPY` step, which can prevent later build steps from reusing the same cached chain
- [ ] `RUN` instructions cannot use the build cache
- [ ] `COPY` should never appear before `CMD`

### 4. How could we improve the Dockerfile from the previous question?

- [ ] Install Flask before copying the frequently changing `app.py`
- [ ] Move `FROM` below `COPY`
- [ ] Remove `WORKDIR`
- [ ] Run `pip install flask` after `CMD`

### 5. You change only `app.py` and rebuild your image. Docker shows:

```text
CACHED [2/5] WORKDIR /app
CACHED [3/5] RUN pip install flask
       [4/5] COPY app.py .
```

What does `CACHED` mean?

- [ ] Docker downloaded those steps from Docker Hub
- [ ] Docker skipped the instructions completely when the image was first built
- [ ] Docker could reuse previous build results instead of repeating the unchanged work
- [ ] Docker stored those instructions in the container's writable layer

### 6. Once an image has been built, what is true about its filesystem layers?

- [ ] They are read-only
- [ ] Every container receives its own complete copy of them
- [ ] Containers modify them directly
- [ ] They exist only while a container is running

### 7. A file called `/app/config.txt` comes from a read-only image layer. A running application modifies that file. What happens?

- [ ] Docker modifies the original image
- [ ] Docker rebuilds the image automatically
- [ ] Docker stores the changed version in the container's writable layer
- [ ] Docker uploads the changed file to Docker Hub

### 8. What does this command do?

```bash
docker container logs docker-web
```

- [ ] Shows the Dockerfile used to create the image
- [ ] Shows log output produced by the container
- [ ] Shows the image's filesystem layers
- [ ] Shows every Docker command previously executed

### 9. What does each part of this image name represent?

```text
username/docker-web-app:latest
```

- [ ] `username` is the container name, `docker-web-app` is the image ID and `latest` is the Docker version
- [ ] `username` is the Docker Hub namespace, `docker-web-app` is the repository name and `latest` is the tag
- [ ] `username` is the image tag, `docker-web-app` is the container and `latest` is the registry
- [ ] The three parts are only used locally and have no meaning to Docker Hub

### 10. What is the difference between `docker image push` and `docker image pull`?

- [ ] `push` uploads an image to a registry, while `pull` downloads an image from a registry
- [ ] `push` builds an image, while `pull` creates a container
- [ ] `push` starts a container, while `pull` stops it
- [ ] They perform the same operation

### 11. You push an updated image to Docker Hub and see:

```text
Layer already exists
```

What does this mean?

- [ ] Docker Hub rejected the layer because it is outdated
- [ ] The layer already exists in the registry and does not need to be uploaded again
- [ ] Docker found the layer in the container's writable layer
- [ ] The entire image already exists locally

### 12. You push your updated image to Docker Hub and then remove all local containers and the local image.

What can you do to use that published image again?

- [ ] Recreate the original filesystem layers manually
- [ ] Pull the image from Docker Hub and create a new container from it
- [ ] Recover it from a stopped container
- [ ] Docker images cannot be recovered after being removed locally

---

**See how you did below.**

> [!SUCCESS]- Quiz Answers
> **1. From top to bottom**
>
> Docker processes Dockerfile instructions in order, starting with the base image selected by `FROM` and continuing through the remaining instructions.
>
> **2. The instruction tells Docker what to do, while a filesystem layer can store the resulting filesystem changes**
>
> The instruction and the layer are not the same thing.
>
> For example:
>
> ```dockerfile
> RUN pip install flask
> ```
>
> The instruction tells Docker to install Flask. The resulting filesystem changes can then be stored in a layer.
>
> **3. Changing `app.py` affects the `COPY` step, which can prevent later build steps from reusing the same cached chain**
>
> Each build step depends on the state produced by the steps before it.
>
> If a frequently changing file is copied too early, later work may need to be processed again unnecessarily.
>
> **4. Install Flask before copying the frequently changing `app.py`**
>
> A better order would be:
>
> ```dockerfile
> FROM python:3.13-slim
>
> WORKDIR /app
>
> RUN pip install flask
>
> COPY app.py .
>
> CMD ["python", "app.py"]
> ```
>
> Dependencies determine what order is possible. When the order is flexible, placing less frequently changing work earlier can improve cache reuse.
>
> **5. Docker could reuse previous build results instead of repeating the unchanged work**
>
> The **build cache** allows Docker to reuse previous results when the inputs for those build steps haven't changed.
>
> **6. They are read-only**
>
> Once an image has been built, its filesystem layers are read-only.
>
> Multiple containers can use the same underlying image layers without modifying the original image.
>
> **7. Docker stores the changed version in the container's writable layer**
>
> This is the basic idea behind **copy-on-write**.
>
> The application reads and writes the file normally. Docker handles the filesystem behavior underneath so that the original file in the image remains unchanged.
>
> **8. Shows log output produced by the container**
>
> ```bash
> docker container logs docker-web
> ```
>
> This lets us inspect the log output produced by the application running inside the container.
>
> **9. `username` is the Docker Hub namespace, `docker-web-app` is the repository name and `latest` is the tag**
>
> The structure is:
>
> ```text
> <username>/<repository>:<tag>
> ```
>
> This allows Docker to associate the image with the correct repository when publishing it to Docker Hub.
>
> **10. `push` uploads an image to a registry, while `pull` downloads an image from a registry**
>
> `push` sends an image from your computer to a registry such as Docker Hub.
>
> `pull` retrieves an image from a registry and stores it locally.
>
> **11. The layer already exists in the registry and does not need to be uploaded again**
>
> Docker Hub can reuse layer content it already has instead of requiring the same content to be uploaded again.
>
> This is different from **build caching**:
>
> **Build cache** avoids repeating unchanged build work on our machine.
>
> **Registry layer reuse** avoids uploading layer content that the registry already has.
>
> **12. Pull the image from Docker Hub and create a new container from it**
>
> Removing the local image does not remove the image that was previously published to Docker Hub.
>
> We can pull it again:
>
> ```bash
> docker image pull <username>/docker-web-app:latest
> ```
>
> and then create a new container from the downloaded image.
>

**Passing grade: 80%**

---

# Part II 

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

Continue on: [[Docker - Module 03]]