---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

You've now learned how to build Docker images, create containers and understand what happens underneath when Docker builds an image.

Now it's time to do it yourself.

In this lab, you'll create a small Python web application and write a **Dockerfile from scratch**.

We'll provide the application code, but **you'll decide how to containerize it**.

> **The goal is simple: create a Python web application, write its Dockerfile, build an image and successfully run the application inside a container.**

Try to complete the lab without looking at the solution at the bottom.

---

# 1. Create the Project

Create a new project directory called:

```text
docker-web-app
```

Inside it, create:

```text
docker-web-app/
 app.py
 Dockerfile
```

---

# 2. Create the Application

We're not testing your Python skills here, so we'll provide the application.
Add the following to `app.py`:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Docker!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

This creates a small Flask web application that listens on port `5000`.

> [!NOTE]
> `host="0.0.0.0"` allows the application to receive connections from outside the container.

Your application is ready.

---

# 3. Write the Dockerfile

Your Dockerfile starts completely empty.
Your application needs Python to run. Visit the official Python image on Docker Hub and find the **`3.13-slim`** tag: https://hub.docker.com/_/python

Now complete the Dockerfile below by replacing each `???` with the correct **Dockerfile instruction**.

```dockerfile
??? ???

??? ???

??? pip install flask

??? app.py .

??? ["python", "app.py"]
```

---

# 4. Build the Image

Your Dockerfile is ready. Now it's time to build the image.

Since we'll publish this image to **Docker Hub in the next lab**, let's name it using Docker Hub's repository format from the beginning:

```text
<username>/<image-name>:<tag>
```

Once the build finishes, verify that your image exists locally.

> [!QUESTION]
> Which command do you need to build the image with the correct name?
>
> How can you verify that the image was created?
---

# 5. Run the Application

Now create a container from your new image.
Your application listens on port:

```text
5000
```

Make the application accessible through port `5000` on your computer as well.
Run the container in **detached mode**. You can give it the same from previous modules.

> [!QUESTION]
> Which options do you need to:
>
> - Run the container in the background?
> - Give the container a name?
> - Connect port `5000` on your computer to port `5000` in the container?

---

# 6. Test the Application

Open your browser and visit:

```text
http://localhost:5000
```

You should see:

```text
Hello from Docker!
```

If you do, congratulations - you've created and containerized a web application from scratch.

But before moving on, verify that the container is actually running.

> [!QUESTION]
> Which Docker command can you use to confirm that `docker-web` is running?

---

# 7. Inspect the Logs

Finally, inspect the logs produced by your application.
You should be able to find output from the Flask server.

> [!QUESTION]
> Which Docker command lets you view the logs produced by a container?

---

# Solution

> [!SUCCESS]- Show Solution
>
> ## 1. Project
>
> Your project should contain:
>
> ```text
> docker-web-app/
> ├── app.py
> └── Dockerfile
> ```
>
> ## 2. Dockerfile
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
> ## 3. Build the Image
>
> From inside the project directory:
>
> ```bash
> docker image build -t docker-web-app .
> ```
>
> Verify it:
>
> ```bash
> docker image ls
> ```
>
> ## 4. Run the Container
>
> ```bash
> docker container run -d --name docker-web -p 5000:5000 docker-web-app
> ```
>
> ## 5. Verify the Container
>
> ```bash
> docker container ls
> ```
>
> You should find:
>
> ```text
> docker-web
> ```
>
> ## 6. Test the Application
>
> Visit:
>
> ```text
> http://localhost:5000
> ```
>
> You should see:
>
> ```text
> Hello from Docker!
> ```
>
> ## 7. Inspect the Logs
>
> ```bash
> docker container logs docker-web
> ```

---

# What Did We Practice?

This time, you were given the **application**, but you had to figure out how to containerize it yourself.

You practiced how to:

- Create a Docker project from scratch.
- Write a Dockerfile.
- Choose and order Dockerfile instructions.
- Build your own Docker image.
- Create a container from that image.
- Publish a container port to your computer.
- Verify that the container is running.
- Inspect the application's logs.

Most importantly, you went from **application code to a running container** without being given the Docker commands along the way.

---

# What's Next?

Our application works locally, but right now the image exists only on our computer.

In the next practice module, we'll take this image and **publish it to Docker Hub** so it can be stored and pulled from a container registry.

Continue with: [[Infrastructure/Docker/module-02/Docker - Hands-On Container Practice 05]]