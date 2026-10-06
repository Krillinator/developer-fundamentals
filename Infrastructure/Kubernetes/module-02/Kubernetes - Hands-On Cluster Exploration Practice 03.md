---
icon: LiTestTube2
---
# Overview

![[lab-image.jpg]]

In the previous module, we used `kubectl` to explore what was happening inside our Kubernetes cluster.

This time, you're going to do the exploring.

The goal isn't to remember every command. 
Instead, we'll practice using **kubectl's built-in help** to figure out commands ourselves.

---

# 1. Ask kubectl for Help

Let's start with something simple.

Try:

```bash
kubectl -h
```

or:

```bash
kubectl --help
```

Take a look through the available commands.

You should recognize some of them from the previous module, but don't worry about understanding everything. 


> [!TIP] Learn to find, not memorize
> One of the most useful skills when working with command-line tools is knowing **how to find what you need yourself**.
>
> The built-in help already contains the commands, options and explanations you need.
>
> Getting comfortable navigating it might feel slower at first, but **it will save you time in the long run** whenever you forget a command or encounter something new.


---

### Your first task

In the list of commands you just got back find the command used to: 
	**display one or more Kubernetes resources**.

> [!TIP]
> We're not looking for a specific resource yet. First figure out **which command lets us retrieve resources**.

> [!SUCCESS]- How do I know I'm done?
> If you've found the right command, its help page should begin with something similar to:
>
> ```text
> Display one or many resources
> ```
>
> **Don't move on until you've found it.**

Once you think you've found it, ask that command for help as well:

```text
kubectl <command> --help
```

This is a showcase to how flexible the command can be. You should be able to find an important command for the next step at `# List all pods in ps output format`

See if you can find it!

> [!SUCCESS]- Answer
>  List all pods in ps output format
> `$ kubectl get pods`


---

# 2. Finding Our Pods

Now that you've discovered the `get` command, let's put it to use.

Try:

```bash
kubectl get pods
```

Hmm... not much to see:

```text
No resources found in default namespace.
```

But we know from the previous module that our cluster is already running several **system Pods**. So where are they?

Notice what the message tells us:

```text
default namespace
```

`kubectl` is only showing us Pods from the **default Namespace**. What we really want is to see Pods from **all Namespaces**.

Instead of giving you the option, let's see if we can find it.

```bash
kubectl get --help
```

There's quite a lot of information here, so rather than reading through everything, let's learn another useful command-line skill: **searching command output**.

> [!TIP]- Searching Command Output
> We know we're looking for something related to Namespaces, so let's filter the help output for `namespace`.
>
> **macOS / Linux**
> ```bash
> kubectl get --help | grep namespace
> ```
>
> **Windows PowerShell**
> ```powershell
> kubectl get --help | Select-String namespace
> ```
>
> Filtering output like this is often much faster than manually searching through a long help page.

Look through the results. Can you find an option that allows `kubectl` to retrieve resources from **all Namespaces**?

Once you've found it, complete the command:

```text
kubectl get pods <option>
```

> [!SUCCESS]- How do I know I'm done?
> Your terminal should suddenly become a lot busier.
>
> Among the results, you should find Pods such as:
>
> ```text
> coredns-...
> kube-apiserver-...
> kube-controller-manager-...
> kube-proxy-...
> kube-scheduler-...
> ```
>
> These are some of the **system Pods we found in the previous module**. You've now figured out how to find them yourself.

---

# 3. Where Are Our Pods Running?

We've found the system Pods running across our cluster, but remember that **every Pod has to run on a Node**, and our current output doesn't make that relationship very obvious, so let's see if `kubectl get` can give us more information.

Ask it for help again:

```bash
kubectl get --help
```

This time, search the output for:

```text
wide
```

You should find an option that changes how much information `kubectl get` displays.

See if you can combine it with the command you discovered earlier:

```text
kubectl get pods <all-namespaces-option> <output-option>
```

> [!SUCCESS]- Answer
> All namespaces using `-A` and with more information using `-o wide`
>
> ```text
> kubectl get pods -A -o wide
> ```

---

# 4. Finding CoreDNS

Now that we can see **which Node each Pod is running on**, let's narrow our investigation down to the **CoreDNS Pods** we worked with in the previous module.

Look through the output and find the Pods whose names begin with:

```text
coredns-...
```

Using only the information already in front of you, figure out:
1. **Which Namespace are the CoreDNS Pods running in?**
2. **Which Node is currently running them?**

> [!SUCCESS]- Answer
> **Namespace:** `kube-system`
>
> **Node:** `kubernetes-practice-02-control-plane`
>
> The exact CoreDNS Pod names may differ in your cluster.

Before moving on, copy the **name of one CoreDNS Pod**. We're going to use it to investigate the Pod in more detail.

---

# 5. Finding a Better Command

So far, `get` has given us a useful **overview** of our resources, but now we want to investigate one CoreDNS Pod in more detail. For that, we'll need another command.

Go back to where we started:

```bash
kubectl --help
```

Look through the available commands and find the one described as:

```text
Show details of a specific resource or group of resources
```

Once you've found it, explore its help page:

```text
kubectl <command> --help
```

Pay particular attention to the **Examples**. 

> [!SUCCESS]- Answer
> The command you're looking for is:
>
> ```bash
> kubectl describe
> ```
>
> `get` gives us an overview of resources, while `describe` gives us **detailed information about a specific resource**.

---

# 6. Investigating CoreDNS

We now have everything we need: the **CoreDNS Pod name**, its **Namespace**, and a command that can investigate individual resources.

See if you can combine them using the following structure:

```text
kubectl <command> pod <pod-name> <namespace-option>
```

If you've forgotten how to specify a Namespace, don't look up the finished command. Use what we've already practiced:

```bash
kubectl <command> --help
```

and search the output for:

```text
namespace
```

> [!SUCCESS]- Answer
> Your command should look similar to:
>
> ``` shell
> kubectl describe pod <coredns-pod-name> -n kube-system
> ```
>
> Your CoreDNS Pod name will be different from the one used by someone else's cluster.
>
> If it worked, your terminal should now contain a **detailed description of the CoreDNS Pod** instead of the short table we've been getting from `get`.

---

# 7. Finding the Evidence

There's a lot of information in front of us now, but we don't need to understand every field. Instead, use the output as evidence and see if you can find:

1. **Which Node is running the Pod?**
2. **What container is inside it?**
3. **Which image is the container using?**
4. **What is its current State?**
5. **Is it Ready?**
6. **How many times has it restarted?**

> [!TIP] Search Instead of Scrolling
> You can use the same filtering technique we've already practiced. For example, on macOS/Linux:
>
> ```bash
> kubectl describe pod <pod-name> -n kube-system | grep Image
> ```
>
> Try replacing `Image` with other things you want to find, such as `State`, `Ready` or `Restart`.

> [!SUCCESS]- Answer
> The exact values can vary between clusters, but you should be able to find fields similar to:
>
> ```text
> Node:           kubernetes-practice-02-control-plane/...
> Containers:
>   coredns:
>     Image:          registry.k8s.io/coredns/coredns:...
>     State:          Running
>     Ready:          True
>     Restart Count:  0
> ```
>
> You've now gone from simply knowing that the Pod **exists** to investigating where it runs, what it contains and whether its container is healthy.

These are the same kinds of details we investigated together in the previous module; this time, the student is finding them independently. :chatgpt-content-reference{index="0"}

---

# 8. Who Is Managing the Pod?

Before leaving our Pod description, there's one more interesting piece of information to find. Look near the top for:

```text
Controlled By:
```

What resource is controlling your CoreDNS Pod?

> [!SUCCESS]- Answer
> You should find something similar to:
>
> ```text
> Controlled By:  ReplicaSet/coredns-...
> ```
>
> The exact ReplicaSet name will vary, but the important part is **`ReplicaSet`**.

We've now discovered that the CoreDNS Pod is being managed by a ReplicaSet: 
	ReplicaSet - CoreDNS Pod

That leaves us with one final question:

> **Who is managing the ReplicaSet?**

---

# 9. Follow the Trail

This time, you're getting less help. You already know how to **retrieve resources, work with Namespaces, investigate individual resources and use `--help` when you're stuck**.

Use those skills to find the **CoreDNS ReplicaSet** mentioned by your Pod and investigate it.

Your goal is simple: find its:

```text
Controlled By:
```

> [!TIP] Stuck?
> Start with the tools you've already practiced:
>
> ```bash
> kubectl --help
> kubectl get --help
> ```
>
> If you're unsure what Kubernetes calls a particular resource, you can also explore:
>
> ```bash
> kubectl api-resources
> ```

> [!SUCCESS]- Answer
> First, you can find the ReplicaSet with:
>
> ```bash
> kubectl get replicasets -n kube-system
> ```
>
> Then investigate the CoreDNS ReplicaSet:
>
> ```bash
> kubectl describe replicaset <corde-dns> -n kube-system
> ```
>
> Look for:
>
> ```text
> Controlled By:  Deployment/coredns
> ```


---

# What Did We Practice?

We haven't really introduced new Kubernetes concepts in this lab. Instead, we've practiced something just as important: **how to find the information we need without already knowing the answer**.

We started with `kubectl --help` and gradually used it to discover commands, find options, filter command output, investigate resources and follow relationships inside our cluster.

When you get stuck later in the course, remember the tools you've practiced here:

```bash
kubectl --help
kubectl <command> --help
kubectl api-resources
```

You don't need to memorize every `kubectl` command. 
Just notice how all the terminologies are applied to what we've covered in the theoretical bits:
* get pods/nodes/dns
* describe core-dns
* kubectl -h help
* namespaces and `kube-system`

---

# What's next?

It's finally time to cover the deployment of our applications.

Continue on: [[Kubernetes - Deploying Our Applications]]