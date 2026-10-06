---
icon: LiHdmiPort
---
# Overview

We know that a **container is an environment created from an image where our application can run**. So far, the applications we've created have simply **run and then stopped**. But not every application works like that. 
Some applications, such as **web servers**, need to stay running and wait for requests.
![[docker-container-what-is-it.avif|483]]

**So to recap:**
Our first container:

```bash
$ docker run hello-world
```

printed a message to then immediately stop.

Our Python application did the same:

```bash
$ docker run my-app
```

However, many applications are supposed to **keep running**. 
Imagine having an online e-commerce shop that simply shut down upon starting it.
Web servers are a perfect example for that.
*Let's run one.*

---

# Run a Web Server

**Remember Docker Hub?**
This time we're going to use an existing image called: **nginx**

Nginx is a **web server**.
[Docker Hub - Nginx](https://hub.docker.com/_/nginx)

Open the terminal and run:

```bash
$ docker run nginx
```

You'll notice something different.
The terminal doesn't immediately return to normal.

**Our container is still running.**
That's because Nginx is a web server and continues running while it waits for requests.

![[docker-nginx-still-running.png|525]]

Notice how we're still inside the process, the server is up and running and we can cancel it at anytime with:

```bash
CTRL + C
```

**This is great!** 
We now have a live web server hosted locally on our computer. 
Let's verify.

---

# Is It Really Running?

Since our terminal is now busy hosting our **Web Server**, we're locked out of using this tab of out terminal. 

Open another terminal *(CTRL + T)* and run:

```bash
$ docker ps
```

You should see something similar to:

![[docker-verify-nginx-still-running.png]]

Our first **long-running container**.

But notice something interesting on column `PORTS`:

```text
80/tcp
```

Nginx is listening for HTTP traffic on **port 80 inside the container**.

**Can we test it?**
The container has its **own isolated environment**.
Our computer doesn't automatically have access to its ports.

> **So how do we reach port 80 from our browser?**

We need to **publish the container's port** to a port on our computer.
We normally cannot enter our website that's hosted through our container unless we publish the port to our local machine's port.

>*"I've hosted web applications before on localhost and that has worked fine. 
>Why this limitation with Docker?"*

Now we're entering the fascinating world of **isolated** enviornments
**Consider this**: You have your own web server that's running on your computer. 
It is hosted locally on localhost, and is accessible through port 80 via: `localhost:80`. 

![[docker-own-pc-localhost-port-80-accessible.png]]

**So no problems right?** 
Exactly, so far all we're doing is self-hosted an application within localhost on our PC. 

> *"Yeah.. and Docker exists on our computer too, so we should be able to access it, right?"*

Let's recap:
Docker **is running on our computer**, but the container it creates don't simply become normal applications running directly on our computer's network.

Instead, Docker gives containers their own **isolated environment**, including their own network.

![[docker-container-localhost-port-80-not-accessible.png]]

So even though both exist on the same computer, these are **not the same port 80**. 

When we connect within our webbrowser we're essentially:

``` text 
Our computer --> localhost:80
```

But Nginx is currently listening here: 

``` text 
Container --> port 80
```

Docker's isolation prevents traffic from simply crossing between them automatically.
And that's exactly what **port publishing** allows us to do.


---

# Stop the Container

Before fixing that, let's stop our current container.

Find it:

```bash
$ docker ps
```

Then:

```bash
$ docker stop <container-id>
```

The container stops, but remember:

> **Stopped ≠ Removed**

You can still find it with:

```bash
$ docker ps -a
```

---

# Publish the Port

We need to tell Docker that traffic arriving on a port on **our computer** should be forwarded to port `80` inside the **container**. 

Now run Nginx again, but this time add:

```bash
$ docker run -p 8080:80 nginx
```

The `-p` stands for **publish**.

> **`-p 8080:80` = Forward traffic from our computer's port 8080 to the container's port 80.**

[Docker Docs - Publishing Ports](https://docs.docker.com/get-started/docker-concepts/running-containers/publishing-ports/)

---

# Open the Website

Now open your browser and go to:

[http://localhost:8080](http://localhost:8080)

You should see:

![[docker-nginx-port-publish-visit-site.png]]

*That's it!* 

---

# Why Port 8080?

We could technically publish Nginx using:

```bash
$ docker run -p 80:80 nginx
```

We just showcased that the two ports **don't have to be the same**.
Nginx still listens on port `80` inside its container.

That means that the number to the **left** is our machine's port connecting to the **container**'s port on the **right**.

This also means different containers can use the **same internal port** while being exposed through different ports on our computer:

```text
localhost:8080  
 Container A : 80

localhost:8081  
 Container B : 80
```

That's another benefit of the container's isolated network.

---

# Check Docker

While Nginx is running, open another terminal and run:

```bash
$ docker ps
```

Look at the `PORTS` column again.

Previously we saw:

```text
80/tcp
```

Now you should see something showing that port `8080` on our computer is being forwarded to port `80` in the container.

![[docker-verify-nginx-still-running-after-publishing-port.png]]

> [!info] Why Does Docker Show `0.0.0.0:8080`?
> `0.0.0.0` means Docker published port `8080` on **all IPv4 network interfaces** on our computer.
>
> That's why we can access it through:
>
> `localhost:8080`
>
> `[::]` is the IPv6 equivalent.
> 
> That can include your Wi-Fi/LAN interface, not only `localhost`. Whether another device can actually reach it also depends on things like your firewall and surrounding network.

> [!success] Remember
> Containers have their **own isolated network and ports**.
>
> `-p` lets us explicitly publish a container port to our computer.

---

# Stop the Container

When you're finished, return to the terminal running Nginx and press:

```shell
CTRL + C
```

Or find it from another terminal:

```bash
$ docker ps
```

and stop it with:

```bash
docker stop <container-id>
```

Remember:

> **Stopped ≠ Removed**

You can still find stopped containers with:

```bash
docker ps -a
```

---

# What's Next?

We've gone from simply running a container:

```bash
docker run nginx
```

to **configuring how that container runs**:

```bash
docker run -p 8080:80 nginx
```

But ports are only one thing we can configure.
Containers can also have their own **names, environment variables, storage and networks**.

What does that mean? [[Infrastructure/Docker/module-01/containers/Docker - Understanding Containers]]