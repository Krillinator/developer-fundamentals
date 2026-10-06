---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

In the previous two labs, you built a Docker image from scratch and published it to Docker Hub.
Now we're going to simulate something that happens constantly in software development:

> **The application has changed and we need to publish a new version.**

You'll modify the application, rebuild the image and publish the updated version to Docker Hub.
Then we'll go one step further.

You'll remove the containers and images from your computer completely and pull the application back down from Docker Hub.

> **The goal is to complete the full workflow: change, rebuild, publish, clean up, pull and run the updated application.**

Try to complete the lab without looking at the solution at the bottom.

---

# 1. Change the Application

Open your existing:

```text
app.py
```

Right now, the application should return:

```text
Hello from Docker!
```

Change it to:

```text
Hello again from Docker!
```

Save the file.

> [!QUESTION]
> We've changed `app.py`, but have we changed the Docker image yet?

---

# 2. Rebuild the Image

Changing the source file on your computer does **not** automatically change the image we built earlier.
The image needs to be rebuilt.

Rebuild:

```text
<username>/docker-web-app:latest
```

using the same name and tag as before.

Pay attention to the build output.
Some previous build steps may be reused from the **build cache**, while the step involving our changed `app.py` needs to reflect the new file.

> [!QUESTION]
> Which command rebuilds the image?
>
> Why can Docker reuse some of the previous build work even though `app.py` changed?

---

# 3. Run the Updated Application

Before publishing anything, make sure the new image actually works.
Your previous container may still exist and may still be using port `5000`.
Use what you've learned to deal with the old container and start a new container from the rebuilt image.

Then visit:

```text
http://localhost:5000
```

You should now see:

```text
Hello again from Docker!
```

> [!QUESTION]
> How can you replace the old container with one created from the newly rebuilt image?
>
> How can you verify that the new container is running?

---

# 4. Publish the Update

The updated image works locally.
Now publish the new:

```text
<username>/docker-web-app:latest
```

to Docker Hub.

Because we're publishing the same repository and tag again, `latest` will now refer to the newly pushed version.
Watch the push output carefully.
You may notice that Docker Hub already has some of the image's layers.

> [!QUESTION]
> Which command publishes the updated image?
>
> Why might some layers not need to be uploaded again?

---

# 5. Remove Everything Locally

Now let's prove that Docker Hub really has our updated application.
Remove the container you created during this lab.

Then remove the local:

```text
<username>/docker-web-app:latest
```

image.

When you're finished, verify that the container and image are no longer available locally.

> [!QUESTION]
> How can you verify that the container has been removed?
>
> How can you verify that the image has been removed?

> [!IMPORTANT]
> Don't delete the repository from Docker Hub.
>
> We only want to remove the resources stored **locally on our computer**.

---

# 6. Pull the Image From Docker Hub

At this point, the image should no longer exist locally.
But we published it to Docker Hub before deleting it.

Now retrieve:

```text
<username>/docker-web-app:latest
```

from Docker Hub.

Watch what Docker does while downloading the image.
Once the pull finishes, verify that the image exists locally again.

> [!QUESTION]
> Which command downloads an image from a registry?
>
> How can you verify that the image is back on your computer?

---

# 7. Run the Pulled Image

There's one final test.
Create a new container from the image you just pulled from Docker Hub.
Remember that the application needs to be accessible through:

```text
http://localhost:5000
```

Open the application in your browser.

You should see:

```text
Hello again from Docker!
```

If you do, you've proven that the updated application was successfully:

```text
rebuilt
published
removed locally
downloaded from Docker Hub
run again
```

The container you're running now was created from the image you **pulled from the registry**, not the local image you originally built.

---

# Solution

> [!SUCCESS]- Show Solution
>
> ## 1. Change the Application
>
> Change:
>
> ```python
> return "Hello from Docker!"
> ```
>
> to:
>
> ```python
> return "Hello again from Docker!"
> ```
>
> Saving `app.py` only changes the source file on your computer. The existing Docker image remains unchanged until we rebuild it.
>
> ## 2. Rebuild the Image
>
> ```bash
> docker image build -t <username>/docker-web-app:latest .
> ```
>
> Docker may reuse cached results for unchanged build steps. The changed `app.py` means the filesystem content produced when copying that file must reflect the new version.
>
> ## 3. Run the Updated Application
>
> First, remove the previous container if it still exists:
>
> ```bash
> docker container rm -f docker-web
> ```
>
> Create a new container from the rebuilt image:
>
> ```bash
> docker container run -d --name docker-web -p 5000:5000 <username>/docker-web-app:latest
> ```
>
> Verify it:
>
> ```bash
> docker container ls
> ```
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
> Hello again from Docker!
> ```
>
> ## 4. Publish the Update
>
> ```bash
> docker image push <username>/docker-web-app:latest
> ```
>
> Docker Hub may already contain some of the unchanged layers from the previous version, so identical layer content doesn't need to be uploaded again.
>
> ## 5. Remove Everything Locally
>
> Remove the container:
>
> ```bash
> docker container rm -f docker-web
> ```
>
> Remove the local image:
>
> ```bash
> docker image rm <username>/docker-web-app:latest
> ```
>
> Verify that the container is gone:
>
> ```bash
> docker container ls -a
> ```
>
> Verify that the image is gone:
>
> ```bash
> docker image ls
> ```
>
> ## 6. Pull the Image
>
> ```bash
> docker image pull <username>/docker-web-app:latest
> ```
>
> Verify that it exists locally again:
>
> ```bash
> docker image ls
> ```
>
> ## 7. Run the Pulled Image
>
> ```bash
> docker container run -d --name docker-web -p 5000:5000 <username>/docker-web-app:latest
> ```
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
> Hello again from Docker!
> ```

---

# What Did We Practice?

This lab brought together the complete image workflow you've learned so far.

You practiced how to:

- Change an application after an image has already been built.
- Rebuild an existing image.
- Recognize where Docker can reuse cached build work.
- Test an updated image before publishing it.
- Push an updated image to Docker Hub.
- Remove containers and images from your local machine.
- Pull an image from a container registry.
- Create a new container from a pulled image.

Most importantly, you proved something for yourself:

> **The image stored on Docker Hub exists independently from the image stored on your computer.**

You built and published the updated image, deleted your local copy and then recovered the same updated application from Docker Hub.

---

# What's Next?

You've now completed all three hands-on practice modules.
You built an application from scratch, published it to Docker Hub, changed and republished it, removed everything locally and finally pulled the updated image back down.
Now we're going to switch back to **theory**.

The next module will test whether you understand what was happening underneath the commands you just used.

We'll revisit concepts such as:
- Images and containers
- Dockerfile instructions
- Image layers
- Build caching
- Read-only and writable layers
- Copy-on-write
- Docker Hub and registries
- Local images vs published images

Instead of following another workflow, you'll be given questions and scenarios where you'll need to explain **why Docker behaves the way it does**.

> **You've practiced the commands. Now let's make sure you understand what's happening underneath them.**

Continue with: [[Infrastructure/Docker/module-02/Docker - Knowledge Check 02]]