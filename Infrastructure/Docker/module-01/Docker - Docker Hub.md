---
icon: LiSearch
---
# Overview

In the previous section, our Dockerfile started with:

```dockerfile
FROM python:3.13-slim
```

**Where did `python:3.13-slim` actually come from?**


---

# Open Docker Hub

Go to: https://hub.docker.com/

Docker Hub is an online place where Docker images can be **published and downloaded**.

Remember when we ran:

```bash
$ docker run hello-world
```

and Docker automatically downloaded `hello-world`?

Docker Hub is where that image came from.

---

# Find Python

Instead of giving Docker an image this time, let's find one ourselves.

Use the search bar and search for:

```text
python
```

You'll see something like:

![[docker-hub-search-python.png|700]]

> [!warning] Which Python Image?
> Docker Hub may show several Python images:
>
> **python - Docker Official Image**  
> **This is the one we want.**
>
> Look for the **Docker Official Image** badge when following this guide.

> [!info] Docker Hardened Image vs Docker Official Image
> You may notice that Docker recommends a **Hardened Image**.
>
> A **Docker Hardened Image (DHI)** is a security-focused version designed to reduce potential vulnerabilities. It removes unnecessary packages and components, resulting in a smaller **attack surface**.
>
> So why aren't we using it?
>
> For now, we're learning Docker using the standard **Docker Official Image**:
> > **Python - Docker Hardened Image** - Security-focused alternative  
> > **python - Docker Official Image** - Standard image used in this guide
>
> The hardened image isn't *better Python*. It's simply packaged with stricter security in mind.
>
> We'll return to hardened images when we start looking at **container security**.

Make sure you click on the **actual name** and not the 'official docker images' text, as that will link you elsewhere.

If you scroll down a bit you'll find the instructions on how to setup your Dockerfile.
https://hub.docker.com/_/python#how-to-use-this-image 

---

# Your Turn

Let's see if you can find an image yourself.

Imagine we're starting a new Python project with these requirements:

> **Python 3.14**  
> **A smaller, lightweight image**

Your task is to:

1. Find the available **tags**
2. Look for **Python 3.14**
3. Find a smaller variant called **`slim`**
4. Figure out the full image name you would use with `FROM`

Try to find it **without looking at the answer below**.

> [!question]- Show Answer
> ```dockerfile
> FROM python:3.14-slim
> ```

If you found it yourself, you've just learned how to **find and select a Docker image for a project** rather than simply copying one from a tutorial.

> [!info]- Understanding Image Tags
> Docker image tags often contain extra words describing the **variant** you're getting.
>
> **`slim`** - Smaller image with fewer unnecessary packages.
>
> **`trixie`** - Built on Debian 13, codenamed *Trixie*.
>
> **`bookworm`** - Built on Debian 12, codenamed *Bookworm*.
>
> **`rc`** - *Release Candidate*. A version being tested before its final stable release.
>
> For example:
>
> `python:3.14-slim` = **Python 3.14 using a smaller, lightweight base.**
>
> You don't need to memorize these. The important skill is being able to **recognize them and look them up when choosing an image.**

---

# Find Our Image

Open the Python image.

You'll notice that there isn't just one version of Python.

There are many different **tags**, such as:

```text
3.13
3.13-slim
3.13-bookworm
3.13-alpine
```

Search the page for:

```text
3.13-slim
```

Recognize it?

That's exactly what we used:

```dockerfile
FROM python:3.13-slim
```

The **image** tells Docker what we want.

The **tag** tells Docker which version or variant we want.

> **`python:3.13-slim` = Python image using the `3.13-slim` tag.**

[Docker Docs - Image Tags](https://docs.docker.com/reference/cli/docker/image/tag/)

> [!info] What About 'latest'?
> If you don't specify a tag, Docker uses the tag **`latest`** by default.
>
> These are equivalent:
>
> ```bash
> $ docker run python
> ```
>
> ```bash
> $ docker run python:latest
> ```
>
> Despite the name, `latest` does **not necessarily mean "the newest version available."**
>
> It's simply a tag named `latest` that the image publisher decides what to point to.
>
> **For predictable projects, prefer a specific version:**
>
> `python:3.14-slim` <-- 
> `python:latest` X
>
> This prevents your project from unexpectedly using a different Python version later.

---
# What's Next?

We now know where this came from:

```dockerfile
FROM python:3.13-slim
```

And we've discovered that Docker Hub contains thousands of images that we can download and use.

Let's dive deeper into what Containers are [[Infrastructure/Docker/module-01/containers/Docker - Container Ports]]