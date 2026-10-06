---
icon: LiFile
---
# Overview

![[dockerfile-icon.png|277]]

We've just covered the relationship between

```text
Image <--> Container
```

But that leaves one important question:

> **Where do images come from?**

That's where a **Dockerfile** comes in.

---

# What Is a Dockerfile?

A Dockerfile is simply:

> **A set of instructions Docker uses to build an image.**

Without a Dockerfile, we won't be able to generate an **image**. 

---

# Create a Small Application

Create a folder:

```text
docker-hello/
```

Inside it, create:

```text
app.py
```

Add:

```python
print("Hello from my own Docker image!")
```

Right now this is just a normal Python application.

---

# Create the Dockerfile

In the same folder, create:

```text
Dockerfile
```

No file extension.

Your project should look like:

```text
docker-hello/
├── app.py
└── Dockerfile
```

Add:

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

> [!hint] Edit File - Terminal
> On MAC & Linux you can use `nano Dockerfile` to begin editing the file
> Windows can use `notepad Dockerfile`

Don't worry about understanding every line yet.

For now, the important thing is:

> **These are instructions for building our image.**

---

# Build It

Open the terminal inside `docker-hello` and run:

```bash
$ docker build -t my-app .
```

Then check:

```bash
$ docker images
```

You should now see:

```text
my-app
```

**We just created an image.**

---

# Run It

Now:

```bash
$ docker run my-app
```

You should see:

```text
Hello from my own Docker image!
```

And there we have the complete Docker flow:

```text
app.py
   ↓
Dockerfile
   ↓
docker build
   ↓
Image
   ↓
docker run
   ↓
Container
```

> [!success] Remember
> **Dockerfile = instructions**
>
> **Image = packaged application**
>
> **Container = where that image runs**

---

# What's Next?

We used four Dockerfile instructions:

```text
FROM
WORKDIR
COPY
CMD
```

But what do they actually mean? [[Infrastructure/Docker/module-01/Docker - Basic Dockerfile Instructions]]