---
icon: LiTestTubeDiagonal
---
# Overview

![[lab-image.jpg]]

So far, our labs have guided you through each Docker command step by step. This time, we're going to do things a little differently.

You've already learned how to create, inspect, stop and clean up containers. Instead of being given every command, you'll be given a task and expected to figure out which commands you need along the way.

> **The goal is simple: create two containers, verify that they're running, stop them and then remove them completely.**

Try to complete the lab without looking at the solution at the bottom.

---

# 1. Create Two Containers

Start by creating two containers from the **Nginx image**.

We'll give you this one:

```bash
$ docker container run -d --name web-one nginx
$ docker container run -d --name web-two nginx
```

You should now have two containers named:

```text
web-one
web-two
```

From here, you're on your own.

---

# 2. Verify Your Containers

Your first task is to **check which containers are currently running**.

You should be able to find both:

```text
web-one
web-two
```

> [!QUESTION] Your Task
> Which Docker command can you use to list your **running containers**?

---

# 3. Stop the Containers

Now stop both `web-one` and `web-two`.

Don't remove them yet. We specifically want the containers to enter the **stopped** state first.

Once you've stopped them, verify that they are no longer running.

> [!QUESTION] Your Task
> How can you stop both containers?
>
> How can you verify that they have stopped?

---

# 4. Find the Stopped Containers

Your containers have disappeared from the list of running containers, but remember:

> **Stopped does not mean removed.**

Find a way to list your containers so that you can see **stopped containers as well**.

You should find `web-one` and `web-two` with a status showing that they have exited.

> [!QUESTION] Your Task
> Which option can you add to the container listing command to show **all containers**, including stopped ones?

---

# 5. Remove the Containers

Now clean up.

Remove the stopped `web-one` and `web-two` containers completely.

Once you've done that, check your containers one final time and make sure they're actually gone.

> [!QUESTION] Your Task
> Which command can clean up **all stopped containers**?
>
> How can you verify afterward that `web-one` and `web-two` no longer exist?

---

# Solution

> [!SUCCESS]- Show Solution
> ## 1. Create the Containers
>
> ```bash
> docker container run -d --name web-one nginx
> docker container run -d --name web-two nginx
> ```
>
> ## 2. List the Running Containers
>
> ```bash
> docker container ls
> ```
>
> Both `web-one` and `web-two` should appear because they're currently running.
>
> ## 3. Stop the Containers
>
> ```bash
> docker container stop web-one web-two
> ```
>
> Check your running containers again:
>
> ```bash
> docker container ls
> ```
>
> `web-one` and `web-two` should no longer appear because they are stopped.
>
> ## 4. Find the Stopped Containers
>
> ```bash
> docker container ls -a
> ```
>
> The `-a` option shows **all containers**, including stopped ones. You should now find `web-one` and `web-two` with an exited status.
>
> ## 5. Remove the Containers
>
> ```bash
> docker container prune
> ```
>
> Confirm the cleanup when Docker asks you to continue.
>
> Finally, check again:
>
> ```bash
> docker container ls -a
> ```
>
> `web-one` and `web-two` should now be gone completely.

---

# What Did We Practice?

This time, you weren't given the commands as you worked through the lab. You had to decide which Docker commands matched each task.

You practiced the complete container lifecycle we've learned so far:
CREATE -> RUNNING -> FILTER -> STOPPED -> REMOVED

Whenever you're ready, head on over to [[Infrastructure/Docker/module-01/containers/Docker - Knowledge Check 01]]