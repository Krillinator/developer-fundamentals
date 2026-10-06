---
icon: LiWorkflow
---
# Overview

![[docker-banner.avif]]

In Module 01, we learned how Docker works from the outside. We downloaded images, created containers, explored isolation and practiced managing the container lifecycle.

Now we're going to switch perspectives.

> **What does Docker look like when we're the ones building the application?**

We'll start with a small Python application that runs normally on our computer. Then we'll package it into a Docker image, run it as a container and eventually push that image to a registry so it can be downloaded somewhere else.

Along the way, we'll also see what happens when our application changes and take a closer look at how Docker images are actually built.

---

# Prerequisites

Before continuing:

- Complete **Module 01**
- Make sure Docker Desktop is running
- Open a terminal

If you haven't completed the previous module yet, start here: [[Docker - Start Here]]

---

# Start Without Docker

Before we put an application inside Docker, let's create one **without Docker**.

Create a new project directory:

```bash
mkdir docker-python-app
cd docker-python-app
```



Once you stand inside the folder, inside of the terminal: add the following:

```python
echo 'from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "hello world!"

if __name__ == "__main__":
    app.run(host="0.0.0.0")' > app.py
```

This is a **web application** using Flask with Python. 
All we have to know right now is that we've created a web application file.

In short: this is a simple Python app that uses Flask to expose an HTTP web server on port 5000.


---

# Create the Dockerfile

Docker needs instructions describing how our image should be built.

Create a file named:

```shell
touch Dockerfile
```

Our project should now look like this:

```text
docker-python-app/
	 app.py
	 Dockerfile
```

Add:

```dockerfile
FROM python:3.6.1-alpine

WORKDIR /app

RUN pip install --upgrade pip
RUN pip install flask

COPY app.py .

CMD ["python", "app.py"]
```

> [!NOTE]- What is Alpine?
> **Alpine Linux** is a lightweight Linux distribution commonly used for Docker images.
>
> When we use:
>
> ```dockerfile
> FROM python:3.13-alpine
> ```
>
> we're starting with a small Alpine Linux environment that already has **Python 3.13** installed.
>
> Alpine helps keep images small, but because it includes fewer tools and libraries by default, some packages can require additional setup compared with images such as `python:3.13-slim`.

We've seen these instructions before:

```text
FROM      Choose the starting image
WORKDIR   Set the working directory
COPY      Copy files into the image
CMD       Define what runs when the container starts
```

The difference is that we're now using them to package **our own application**.

---

# Build the Docker Image

We have our Python application and our Dockerfile.

Now Docker can use those instructions to build an image.

Run:

```bash
docker build -t docker-python-app .
```

Once the build finishes, check your images:

```bash
docker images
```

You should find:

![[docker-images-with-tag-name-latest-terminal.png]]

Our Python application now exists as a **Docker image**.

> [!NOTE] What does `-t` mean?
> `-t` lets us give the image a **name and optionally a tag**.
>
> In this case:
>
> ```text
> docker-python-app
> ```
>
> becomes the name of our image.

> [!NOTE]- What is :latest tag?  
> `latest` is simply Docker's **default tag** when no tag is specified.
> 
> ```text
> docker-python-app = docker-python-app:latest
> ```
> 
> Despite the name, `latest` does **not** automatically mean the newest version. It's just a tag named `latest`.

---

# Run Our Image

Now that we've built the image, let's create a container from it:

```bash
docker run -p 5000:5000 -d docker-python-app
```

> [!warning]- Apple Silicon Mac Users
> If you're using an **Apple Silicon Mac (M1, M2, M3, etc.)**, you may see a warning like:
>
> ```text
> The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8)
> ```
>
> This happens because the older `python:3.6.1-alpine` image uses the **AMD64** architecture, while Apple Silicon uses **ARM64**.
>
> Docker Desktop can emulate AMD64, so the container can still run. We'll use newer images later that support ARM64 natively.

**So now what?**
The first time, Python and our application ran directly from the files on our computer.

This time, Docker created a **container from the image we built** and ran the application inside that container.

We have now successfully gone through the basic build process ourselves.

**Visit the website**
Go to localhost:5000 and check if you can visit the website. It should look something like:

![[docker-run-port-detached-web-app-web-browser-result.png|513]]

**Success!**

---

# Docker Container Logs

Now that we've both built, and ran the image, let's log the results after having visited the web application.

If you want to see logs from your application you can use the docker command:

```bash
$ docker container logs <container-id>
```

By default, `docker container logs` prints out what is sent to standard out by your application. 

![[docker-container-logs-terminal-result.png]]

The Dockerfile is used to create reproducible builds for your application. 
A common workflow is to have your CI/CD automation run `docker image build` as part of its build process. After images are built, they will be sent to a central registry where they can be accessed by all environments (such as test environment) that need to run instances of that application.

---

# What Did We Learn?

We started with a small Python web application and packaged it ourselves using Docker.

Along the way, we:

- Created a Flask application
- Wrote a `Dockerfile`
- Built our own Docker image
- Learned about image names and tags (`-t`)
- Created a container from our image
- Exposed the application on port `5000`
- Visited the application through our browser
- Inspected the application's container logs (`docker container logs <container-id>`)

---

# What's Next?

Right now, `docker-python-app` only exists on **our computer**.

But what if another developer wanted to use it?

Instead of sending them our files and asking them to build the image themselves, we can publish the image to a **container registry**.

In the next section, we'll push our custom image to **Docker Hub**, where it can be stored and pulled by other developers and environments.

Continue with: [[Infrastructure/Docker/module-02/Docker - Push to Central Registry]]