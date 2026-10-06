---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

In the previous lab, you built your own Docker image:

```text
<username>/docker-web-app:latest
```

Right now, that image exists **only on your computer**.

But what if you wanted someone else to use it? 
Or wanted to pull the same image onto another computer?

That's where a **container registry** comes in.
In this lab, you'll publish your image to **Docker Hub**.

> **The goal is simple: log in to Docker Hub, publish your image and verify that it exists in the registry.**

Try to complete the lab without looking at the solution at the bottom.

---

# 1. Check Your Image

Before publishing anything, make sure the image from the previous lab still exists locally.
You should have an image named:

```text
<username>/docker-web-app:latest
```

where `<username>` is your own Docker Hub username.

> [!QUESTION]
> Which Docker command can you use to list your local images?
>
> Can you find `<username>/docker-web-app:latest`?

---

# 2. Log In to Docker Hub

Before Docker can publish an image to your account, it needs permission to access your Docker Hub account.

Log in to Docker Hub through the terminal.

> [!QUESTION]
> Which Docker command allows you to authenticate with Docker Hub?

Follow the instructions shown in your terminal to complete the login.

> [!NOTE]
> You need a Docker Hub account to complete this lab.
>
> If you don't already have one, create one at:
>
> https://hub.docker.com/

---

# 3. Check the Image Name

Before we publish the image, let's look at its name again:

```text
<username>/docker-web-app:latest
```

Docker Hub uses this naming structure:

```text
<username>/<repository>:<tag>
```

The username tells Docker Hub **which account the repository belongs to**.

Because we already used this naming structure when we built the image in the previous lab, we don't need to rename or tag it again.

> [!QUESTION]
> Does your image begin with your own Docker Hub username?
>
> If it doesn't, you'll need to correct its tag before trying to publish it.

---

# 4. Publish the Image

Your image is ready.
Now publish:

```text
<username>/docker-web-app:latest
```

to Docker Hub.

Watch the terminal while Docker uploads the image.
You may notice messages such as:

```text
Pushed
```

or:

```text
Layer already exists
```

You've already learned why Docker can reuse layers that the registry already has.

> [!QUESTION]
> Which Docker command publishes an image to a container registry?

---

# 5. Verify It on Docker Hub

Publishing from the terminal is only half of the verification.
Open Docker Hub in your browser: https://hub.docker.com/

Navigate to your repositories.
You should now find:

```text
docker-web-app
```

with the tag:

```text
latest
```

The image now exists in **two places**:

```text
Your computer
Docker Hub
```

The local image and the image stored in the registry are separate.
Removing the local image later will **not** remove the image you've published to Docker Hub.

---

# Solution

> [!SUCCESS]- Show Solution
>
> ## 1. Check Your Image
>
> ```bash
> docker image ls
> ```
>
> You should find:
>
> ```text
> <username>/docker-web-app:latest
> ```
>
> ## 2. Log In
>
> ```bash
> docker login
> ```
>
> Follow the instructions in your terminal to authenticate with Docker Hub.
>
> ## 3. Check the Image Name
>
> Your image should follow:
>
> ```text
> <username>/docker-web-app:latest
> ```
>
> Because we built the image using this name in the previous lab, no additional tagging should be necessary.
>
> ## 4. Publish the Image
>
> ```bash
> docker image push <username>/docker-web-app:latest
> ```
>
> Replace `<username>` with your Docker Hub username.
>
> ## 5. Verify It
>
> Open Docker Hub and check your repositories.
>
> You should find:
>
> ```text
> docker-web-app
> ```
>
> with the `latest` tag.

---

# What Did We Practice?

In this lab, you took an image that existed only on your computer and published it to a **container registry**.

You practiced how to:

- Verify that an image exists locally.
- Authenticate with Docker Hub.
- Recognize Docker Hub's image naming structure.
- Push an image to a registry.
- Verify that the published image exists on Docker Hub.

Most importantly:

> **Building an image creates it locally. Pushing an image publishes it to a registry.**

---

# What's Next?

Our application is now running locally **and** its image has been published to Docker Hub.
But applications don't stay the same forever.

In the final practice module, you'll **change the application and publish a new version**.
Then you'll remove your local containers and images completely.

Finally, you'll pull the image back down from Docker Hub and run it again to verify that you're using the newly published version.

Continue with: [[Infrastructure/Docker/module-02/Docker - Hands-On Container Practice 06]]