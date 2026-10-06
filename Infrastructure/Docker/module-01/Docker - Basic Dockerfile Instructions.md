---
icon: LiWholeWord
---
# Overview

In the previous section, we created our first `Dockerfile`:

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

**It worked!**

But we haven't actually explained what any of these instructions mean.

*Let's fix that!*

---

# FROM

```dockerfile
FROM python:3.13-slim
```

`FROM` tells Docker which **image we want to start from**.

Our application **needs** Python to run.

Instead of installing Python ourselves, we're starting with an existing image that already provides Python:

```text
python:3.13-slim
```

Our own image is then built **on top of it**.

```text
python:3.13-slim
        ↓
     Our Image
```

> **`FROM` = What should our image start with?**

---

# WORKDIR

Next we wrote:  
  
```dockerfile  
WORKDIR /app  
```  
  
`WORKDIR` sets the **current directory inside the image**.  
  
This is **not** a folder on our computer. Docker images have their own filesystem.  

![[docker-keyword-workdir-explained.png|252]]
*`This is equivelant to terminal command mkdir && cd app`*

That means that the container will inherit the dockers filesystem.
That way we've given out application a known, organized location inside the image instead of relyin on whatever directory the base image happens to use.

The instructions that follow will now use `/app` as their current directory.  
  
> **`WORKDIR` = Set the current directory inside the image.**

---

# COPY

Next:

```dockerfile
COPY app.py .
```

This copies our `app.py` into the image.

![[docker-keyword-copy-explained.png|285]]

Because our `WORKDIR` is `/app`, the `.` means the current working directory:

```text
Our Computer             Image

app.py       ───────▶    /app/app.py
```

> **`COPY` = What files should we put inside the image?**

---

# CMD

Finally:

```dockerfile
CMD ["python", "app.py"]
```

`CMD` tells Docker what command should run by default when a **container starts** from this image.

In our case, it's essentially:

```bash
python app.py
```

Which produces:

```text
Hello from my own Docker image!
```

> **`CMD` = What should run when the container starts?**

---

# Put It Together

Docker starts with
**`FROM`**, which downloads the Python base image.
**`WORKDIR`** then creates and moves into `/app` inside the image. 
**`COPY`** copies our `app.py` into that folder. 

Finally, when a container is created from the finished image, 
**`CMD`** runs `python app.py`, which starts our Python program.

> [!success] Remember
> A **Dockerfile** describes how Docker should build an image.
>
> These are only some of the available Dockerfile instructions. We'll introduce others when we actually need them.

---

## Official Documentation

[Docker - Dockerfile Reference](https://docs.docker.com/reference/dockerfile/)

[Docker - FROM](https://docs.docker.com/reference/dockerfile/#from)

[Docker - WORKDIR](https://docs.docker.com/reference/dockerfile/#workdir)

[Docker - COPY](https://docs.docker.com/reference/dockerfile/#copy)

[Docker - CMD](https://docs.docker.com/reference/dockerfile/#cmd)

---

# What's Next?

We now know how this:

```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

becomes:

```text
Dockerfile → Image → Container
```

But there's something interesting hiding in our very first line:

```dockerfile
FROM python:3.13-slim
```

**Where did that Python image come from?**

That's where **Docker Hub** comes in.

Continue with: [[Infrastructure/Docker/module-01/Docker - Docker Hub]]