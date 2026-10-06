---
icon: ☑
---
# Quiz

![[quiz-test-img.jpg|361]]

Before moving on, test what you've learned so far.

### 1. What is a container?

- [ ] A lightweight virtual machine with its own kernel
- [ ] An isolated environment for running processes
- [ ] A copy of the Docker application
- [ ] A type of Linux operating system

### 2. What is the main difference between an image and a container?

- [ ] An image is a running process, while a container stores files
- [ ] An image is a template used to create containers, while a container is a running instance created from an image
- [ ] Images are used on Linux, while containers are used on Windows and macOS
- [ ] There is no difference

### 3. What do Linux namespaces help Docker containers do?

- [ ] Limit how much CPU and memory they can use
- [ ] Download images from Docker Hub
- [ ] Give processes an isolated view of parts of the system
- [ ] Store container data permanently

### 4. What does a PID namespace isolate?

- [ ] Network ports
- [ ] Processes and process IDs
- [ ] CPU and memory
- [ ] Files stored inside an image

### 5. What is the main purpose of cgroups?

- [ ] Control how many resources processes can use
- [ ] Control what processes can see
- [ ] Create Docker images
- [ ] Connect containers to Docker Hub

### 6. Which statement best describes namespaces and cgroups?

- [ ] Namespaces control resource usage, while cgroups control visibility
- [ ] Namespaces control what processes can see, while cgroups control how many resources they can use
- [ ] They are two names for the same Linux feature
- [ ] Both are used primarily for downloading images

### 7. What does this command do?

```bash
docker container exec -it web-one bash
```

- [ ] Creates a new container called `web-one`
- [ ] Deletes `web-one` and starts Bash
- [ ] Starts Bash as another process inside the running `web-one` container
- [ ] Creates a Bash image from `web-one`

### 8. Why did we use `-d` when starting our Nginx containers?

- [ ] To delete the containers automatically when they stop
- [ ] To run the containers in the background without taking over the terminal
- [ ] To give each container its own kernel
- [ ] To download the image before starting the container

### 9. You stop `web-one` with:

```bash
docker container stop web-one
```

What happens to the container?

- [ ] The container and its image are deleted
- [ ] The container is deleted, but its image remains
- [ ] Its processes stop, but the container still exists
- [ ] Nothing happens until Docker is restarted

### 10. What does this command do?

```bash
docker container prune
```

- [ ] Removes all Docker images
- [ ] Removes all running containers
- [ ] Removes stopped containers
- [ ] Removes Docker itself

---

**See how you did below.**

> [!SUCCESS]- Quiz Answers
> **1. An isolated environment for running processes**
>
> Containers isolate processes while relying on the host's kernel. They are not small virtual machines with their own kernels.
>
> **2. An image is a template used to create containers, while a container is a running instance created from an image**
>
> The same image can be used to create multiple containers.
>
> **3. Give processes an isolated view of parts of the system**
>
> Namespaces help determine what processes inside a container can see.
>
> **4. Processes and process IDs**
>
> A PID namespace gives processes an isolated view of processes and their IDs. This is why the first process inside a container can appear as PID `1`.
>
> **5. Control how many resources processes can use**
>
> cgroups can control and monitor resources such as CPU, memory and the number of processes.
>
> **6. Namespaces control what processes can see, while cgroups control how many resources they can use**
>
> A useful way to remember it is: **namespaces = what can I see?** and **cgroups = how much can I use?**
>
> **7. Starts Bash as another process inside the running `web-one` container**
>
> `docker container exec` runs another command inside an existing running container. It does not create a new container.
>
> **8. Run the containers in the background without taking over the terminal**
>
> `-d` means **detached mode**. This lets the container continue running while we keep using the same terminal.
>
> **9. Its processes stop, but the container still exists**
>
> Stopping and removing are different operations. A stopped container can still be seen with `docker container ls -a`.
>
> **10. Removes stopped containers**
>
> `docker container prune` cleans up stopped containers. It does not remove the images they were created from.

**Passing grade: 80%**

If you passed, continue on to module 02: [[Infrastructure/Docker/module-02/Docker - Module 02]]