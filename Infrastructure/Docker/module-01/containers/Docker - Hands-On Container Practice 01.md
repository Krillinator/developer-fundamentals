---
icon: LiTestTubeDiagonal
---
# Overview

![[lab-image.jpg]]

> **What does a container actually look like while it's running?**

Imagine we start an Ubuntu container and run a normal Linux command inside it.
We've just learned that containers are really **isolated processes**, so let's see that in practice.

---

# 1. Run an Ubuntu Container

Open a terminal and run:

```bash
$ docker container run -t ubuntu top
```

This starts a container using the **Ubuntu image** and runs the Linux `top` command inside it.

The `-t` flag tells Docker to allocate a **pseudo-terminal (TTY)** for the container.

> [!NOTE]- What is a TTY?
> Some programs are designed to run **interactively inside a terminal** rather than simply printing an output and stopping.
>
> `top` is one of those programs. 
> It continuously updates the processes shown on your screen.
>
> The `-t` flag creates a **TTY (terminal)** for the container so `top` can display correctly.

If the Ubuntu image isn't already available locally, Docker will download it first.

> [!NOTE]- Why Ubuntu?
> **Ubuntu** is a Linux distribution.
>
> The Ubuntu image gives our container an Ubuntu-style filesystem and tools that we can experiment with.
>
> It does **not** give the container its own Ubuntu kernel. The container still relies on the kernel underneath it.

You should now see `top` running in your terminal.

![[top-output-terminal.png|700]]

Notice that on the third row it says %Cpu(s): 

> [!NOTE]- What Am I Looking At?
> This is **`top`**, a Linux program used to monitor what's currently happening on a system.
>
> Think of it a little like **Task Manager on Windows** or **Activity Monitor on macOS**.
>
> It continuously shows information about **running processes, CPU usage, memory usage and system load**.
>
> Looking at our output right now:
>
> - **CPU:** About `0.2%` is being used, while `99.6%` is idle.
> - **Memory:** About `586 MB` of `7.8 GB` is currently used.
> - **System load:** `0.00, 0.00, 0.00`, meaning the system currently has essentially no workload waiting for CPU time.
> - **Tasks:** There is `1` visible process, and it is currently running.
>
> At the bottom, we can see that process:
>
> ```text
> PID   USER   %CPU   %MEM   COMMAND
> 1     root   0.0    0.1    top
> ```
>
> `top` is **PID 1**, is running as `root`, and is currently using almost no CPU or memory.
>
> The interesting part is that our computer has many other processes running, but `top` can only see **itself** from inside this container.
>
> This is our **PID namespace isolation** in action.

---

# What Is `top`?

> **If a container is made up of processes running on our computer, can it see the other processes running on our computer too?**

Imagine everything currently running on your computer. 
Your browser is running processes, Docker is running processes, and your operating system has many background processes of its own. There could easily be hundreds of processes running at the same time.

Now look at `top` inside our container. We can currently see only one process:

```text
PID   USER   COMMAND
1     root   top
```

So why can the container only see `top`?

This is where the **PID namespace** we learned about earlier comes into play. 
**PID** stands for **Process ID**, which is simply a number Linux uses to identify a running process. 
This namespace gives the container its own isolated view of processes and their IDs.

In our container, `top` appears as **PID 1** because it is the first process running inside the container. The other processes on our computer are still running, but they aren't visible from inside this container.

Even though we are using the Ubuntu image, it is important to note that the container does not have its own kernel. It uses the kernel of the host and the Ubuntu image is used only to provide the file system and tools available on an Ubuntu system.

---

# 2. Find the Running Container

Leave `top` running and open a **second terminal**.

Run:

```bash
$ docker container ls
```

This displays your **currently running containers**.
You should see the Ubuntu container running `top`.

![[containers-list-sources-result-terminal.png]]

Find your Container ID, it'll be useful for our upcoming step. 

---

# 3. Enter the Running Container

Now let's start another process **inside the same container**.

Run:

```bash
$ docker container exec -it <container-id> bash
```

Your terminal should change to something similar to:

```text
root@b3ad2a23fab3:/#
```

You're now running **Bash inside the container**.

> [!NOTE]- What is Bash?
> **Bash** is a command-line shell commonly found on Linux systems.
>
> Starting Bash gives us a command prompt inside the container where we can run Linux commands.

The `-it` flags allow us to interact with Bash through our terminal:

| Flag | Purpose |
| --- | --- |
| `-i` | Keeps input open so we can type commands |
| `-t` | Gives Bash a terminal (TTY) |

Together, `-it` gives us an **interactive terminal inside the container**.

> [!NOTE]- Why do these flags do?
> Think about what happens when you normally open Terminal on your Mac and start typing commands. You don't just have a way of sending text to a program. You also have an interactive environment that handles things like **showing a prompt, displaying what you type, moving the cursor, using arrow keys and reacting to keyboard shortcuts such as `Ctrl+C`**.
>
> `-i` keeps the **input open**, which allows us to type and send commands to Bash.
>
> `-t` creates the **TTY (terminal)** that provides the interactive terminal behaviour we're used to.
>
> This is why we use them together. `-i` lets us **send input to Bash**, while `-t` lets us **interact with Bash like we normally would through a terminal**.

---

# Recap

`docker container exec` tells Docker to start another process inside an **existing container**.

1. `docker container run` creates and starts a **new container**.
2. `docker container exec` runs another command inside a container that is **already running**.

So our container originally contained the `top` process.
After running `exec`, it also contains our new `bash` process.

```text
Container
├── top
└── bash
```

So then why are we doing this?
Because **`top` is already running as the container's main process**, but it doesn't give us a place to type Linux commands.

---

# 4. Inspect the Container's Processes

Now that we're inside the container, run:

```bash
$ ps -ef
```

> [!NOTE]- What does this mean?
> `ps` displays running processes (process status).
>
> `-e` shows **all processes** the container can see (every).
>
> `-f` displays **more information** about those processes (format).

You should see only a small number of processes, including:

```text
top
bash
ps
```

Remember that our computer itself has many more processes running.
The container simply has an **isolated view** of them.
So even though we asked `ps` to show **all processes**, it can only show the processes visible from inside the container.

This is exactly what we learned about earlier with Linux namespaces.
The **PID namespace** controls which processes the container can see.
Other namespaces isolate other parts of the environment:

| Namespace | Isolates |
| --- | --- |
| `PID` | Processes and process IDs |
| `MNT` | Filesystem mount points |
| `NET` | Networking |
| `IPC` | Communication between processes |
| `USER` | Users and groups |
| `UTS` | Hostname and domain name |

We're now seeing these concepts in practice rather than just reading about them.

![[containers-proces-status-bash-pid-terminal.png|587]]

There are three processes here, and they tell the story of what we've done so far. 
We originally created the container with `top`, which became **PID 1**. 
We then used `docker container exec` to start Bash inside the same container, giving us another process. 
Finally, we typed `ps -ef` into Bash, so Bash started the third process that we're looking at right now.

> [!NOTE] A few useful things to recognize
> **PID** means **Process ID**. 
> It is the number used to identify a process. In our example, `top` is PID `1`, Bash is PID `26` and `ps -ef` is PID `33`.
>
> **PPID** means **Parent Process ID** 
> It tells us which process started another process. `ps -ef` has a PPID of `26`, which matches the PID of Bash. This makes sense because we used Bash to run `ps -ef`.


---

# 5. Exit the Container

Exit Bash:

```bash
exit
```

You're now back in your normal terminal.

> [!NOTE] Did we stop the container?
> No.
>
> `exit` only stopped the **Bash process** we started with `docker container exec`.
>
> The original `top` process is still running, so the container is still running.

You can compare what the container could see with what your computer can see by running:

```bash
ps -ef
```

or:

```bash
top
```

Your computer will have **many more processes** than the container could see.

---

# 6. Stop the Container

Return to the terminal where `top` is still running and press:

```text
Ctrl + C
```

This stops `top` and the container.

This happens because `top` was the main process that we started when we created the container:

---

# What Did We Learn?

We just saw several of our earlier concepts working together in a real Docker container.

- `docker container run` created and started a container.
- `ubuntu` provided the filesystem and Linux tools inside it.
- `top` became the container's main process.
- `-t` gave `top` a terminal.
- Linux namespaces isolated the container's processes.
- `docker container ls` showed our running container.
- `docker container exec` started another process inside it.
- `bash` gave us a shell inside the container.
- `-it` allowed us to interact with Bash.
- `ps -ef` let us inspect the processes visible inside the container.
- The container shared the underlying kernel rather than having its own.

Most importantly:

> **A Docker container isn't a tiny Virtual Machine but an isolated environment containing running processes!**

Let's continue by cleaning up our workspace in [[Infrastructure/Docker/module-01/containers/Docker - Hands-On Container Practice 02]]