---
icon: LiNetwork
---
# Overview

![[undraw_server_9eix.svg|252]]

Our first Swarm service is running.

If we check it:

```bash
docker service ls
```

we currently have:

```text
NAME        MODE         REPLICAS
nginx-web   replicated   1/1
```

And:

```bash
docker service ps nginx-web
```

shows us where that task was scheduled.
So our current desired state is:

```text
nginx-web
Desired replicas: 1
Actual replicas:  1
```

Everything matches.

But why did we build a cluster with **three machines** if we're only going to run one instance of our application?

Let's change that.

---

# Scaling Our Service

Imagine our NGINX service starts receiving more traffic.

Instead of running one instance we want several instances of the same service running across our cluster.

Let's tell Swarm that we now want:

> **3 replicas of `nginx-web`.**

Run this on `node-one`:

```bash
docker service update \
  --replicas 3 \
  nginx-web
```

We could also express the same intent with:

```bash
docker service scale nginx-web=3
```

For now, we'll use `docker service update` because it makes an important idea particularly clear:

> **We're updating the desired state of an existing service.**

---

# What Did We Actually Ask Docker To Do?

We didn't tell Swarm which nodes should run the new containers. We simply changed the **desired state** from **1 replica to 3 replicas**. The manager compares this with the actual state, sees that only **1 replica is running**, and determines that **2 more tasks are needed**. It then schedules those tasks onto eligible nodes in the Swarm.

The web application is now running on three separate containers:

![[docker-swarm-docker-service-ps-nginx-web-terminal-results.png]]

---

# Watch Swarm Reach the Desired State

Run:

```bash
docker service ls
```

You should eventually see:

```text
NAME        MODE         REPLICAS
nginx-web   replicated   3/3
```

This is the reconciliation process we discussed earlier:

```text
Desired state
 3 replicas

Actual state
 1 replica

Manager compares them

 2 tasks are missing

Manager schedules 2 tasks

Actual state
 3 replicas
```

> **Desired state and actual state now match again.**

Because we published the service on port `80:80`, Docker Swarm can now distribute incoming connections across our three NGINX replicas. However, each replica currently returns the same NGINX page, making it difficult to see which one actually handled the request. Let's fix that.

---

# Make Our Replicas Identifiable

If we send several requests to the service, how can we tell whether they were handled by different replicas?

Each of our Ubuntu nodes already has its own hostname:

```text
node-one
node-two
node-three
```

We're going to make NGINX return the hostname of the **node it's running on** instead of its normal webpage.

To do that, we'll mount the node's:

```text
/etc/hostname
```

over NGINX's default:

```text
/usr/share/nginx/html/index.html
```

Update the service:

```bash
docker service update \
  --mount-add type=bind,source=/etc/hostname,target=/usr/share/nginx/html/index.html,readonly \
  nginx-web
```

Swarm will update the service's tasks with this new configuration.

Once the update finishes, check them:

```bash
docker service ps nginx-web
```

Now send a request:

```bash
curl http://localhost
```

Instead of the normal NGINX page, you should get something like:

```text
node-two
```

Run it several times:

```bash
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
curl http://localhost
```

You may see responses such as:

```text
node-two
node-three
node-one
node-two
node-three
```

Now we can actually **see the routing mesh at work**.

A response of:

```text
node-three
```

means the request was ultimately handled by an NGINX task running on `node-three`.

> [!NOTE]
> We're identifying the **node hosting the NGINX task**, not giving each container its own unique ID.
>
> We're only using `/etc/hostname` as a simple lab trick to make Swarm's traffic distribution visible.

---

# Theory: Do We Really Run the Same Application Multiple Times?

We've just created three replicas of the same NGINX service, which might raise a reasonable question: **do we actually run the same application three, five or even ten times in the real world?**

Yes. Running multiple instances of the same application is very common, particularly when we want to handle more traffic or avoid depending on a single running instance.

But a real application usually isn't just one service that we duplicate several times. Imagine that we're building an online store. The system might contain a **frontend** that serves the website, an **API** that handles application logic, and a **database** that stores products, users and orders.

Those are different workloads:

```text
Frontend
API
Database
```

In an orchestrated environment, we can treat them separately. The frontend can be one service, the API another service, and the database another part of the system. Most importantly, **each can have different requirements**.

Imagine we initially deploy our store with only one frontend and one API:

```text
Frontend     1 replica
API          1 replica
Database     1 instance
```

That might work perfectly well while we're developing or running a small application. But there's an obvious weakness: if our only frontend container crashes, there is temporarily no frontend available while Swarm replaces it. The same problem exists with our API.

If availability matters, we might instead decide that we always want at least two frontend instances running:

```text
Frontend     2 replicas
API          2 replicas
Database     1 instance*
```

Now imagine one frontend instance fails. The other frontend can continue serving requests while Swarm notices that the actual state has fallen below the desired state and schedules a replacement.

This is exactly the reconciliation process we've been learning about.

We aren't necessarily creating replicas because one machine isn't powerful enough. Sometimes we're creating them because **we don't want the entire service to depend on one running container**.

**Now imagine our store becomes popular.** 
The frontend is coping perfectly well with two replicas, but our API is receiving thousands of requests for products, shopping carts, searches and orders.

We don't need to duplicate the entire architecture.

We could scale just the API:

```text
Frontend     2 replicas
API          5 replicas
Database     1 instance*
```

There are now two copies of the frontend application and five copies of the API application.

If a user requests:

```text
GET /products
```

the request could be handled by any available API replica. Another user's request might be handled by a completely different replica. They're still using the **same API service**; there are simply multiple instances available to do the work.

This is an important distinction:

> **Scaling doesn't mean copying the entire application. We can scale individual services according to their needs.**

Our frontend might need two replicas primarily for **availability**, while our API might need five replicas for both **availability and capacity**.

**And What About the Database?**
You may have noticed that we've kept writing:

```text
Database     1 instance*
```

with an asterisk.

Wouldn't that database now be a single point of failure?

**Yes.**
But databases introduce another problem: **state**.

Our frontend and API can often be designed so that several interchangeable instances can process requests. A database actually contains persistent data, so simply starting three independent copies doesn't magically give us one highly available database.

Making a database highly available can involve database replication, clustering, failover and rules about which instance is allowed to accept writes. That's its own problem that we won't explore separately this time around.

---

# Rolling Updates

Imagine our API is running with three replicas and users are actively using it. We've just released a new version.

Without orchestration, we might stop all three containers, replace them with the new version and start everything again. But during that process, our application could become unavailable.

Instead, we'd prefer something like this:

> **Replace the old instances gradually while the remaining instances continue serving users.**

This is called a **rolling update**.

---

# Update Our NGINX Service

We can demonstrate this with our existing `nginx-web` service.
First, check what image the service currently uses:

```bash
docker service ls
```

You can also inspect the service directly:

```bash
docker service inspect nginx-web --pretty
```

Our service currently uses:

```text
nginx:latest
```

For the experiment, let's update it to a specific NGINX version:

```bash
docker service update \
  --image nginx:1.28 \
  nginx-web
```

We're changing the desired state of our **existing service**.

---

# What Happens to the Running Containers?

Swarm now needs to replace the existing tasks so that the actual state matches our new desired state.

But instead of replacing everything at once, Swarm performs a **rolling update**.

By default, Swarm updates one task at a time. This means one old task can be replaced while the other replicas remain available.

Imagine we begin with:

```text
Replica 1     Old version
Replica 2     Old version
Replica 3     Old version
```

Swarm replaces them progressively:

```text
Replica 1     New version
Replica 2     Old version
Replica 3     Old version
```

then:

```text
Replica 1     New version
Replica 2     New version
Replica 3     Old version
```

until eventually:

```text
Replica 1     New version
Replica 2     New version
Replica 3     New version
```

The important idea is that **the entire service doesn't need to disappear just because we're deploying a new version**.

---

# Control How the Update Happens

Swarm also lets us control the rollout strategy.

For example:

```bash
docker service update \
  --image nginx:1.28 \
  --update-parallelism 1 \
  --update-delay 10s \
  nginx-web
```

Here:

```text
--update-parallelism 1
```

means Swarm updates **one task at a time**.

And:

```text
--update-delay 10s
```

means Swarm waits **10 seconds between update batches**.
In a real application, these settings give us control over how aggressively a new version is deployed.

---

# What Did We Learn?

A **rolling update** allows us to release a new version of a service gradually instead of replacing every running instance at once.

Swarm can control how many tasks are updated simultaneously and how long it waits between batches. During the rollout, other replicas can remain available to continue handling traffic.

This gives us another piece of the orchestration puzzle:

> **Scaling changes how many instances should run. Rolling updates change what version those instances should run. Swarm manages the transition toward both desired states.**


---

# What's Next?

So far, every change to our service has been intentional. We told Swarm to increase the number of replicas, and later told it to roll out a new version of our application. In both cases, **we changed the desired state**, and Swarm worked to make the cluster match it.

But in production, changes aren't always intentional.

What happens if one of our worker nodes suddenly goes offline and takes a running container with it? We haven't asked for fewer replicas, so our desired state still says:

```text
Desired: 3 replicas
```

but the actual state might suddenly become:

```text
Actual: 2 replicas
```

Will Swarm notice the difference? And if it does, what will it do about it?

Next, we'll deliberately remove a worker from our cluster and watch **reconciliation happen in real time**.

Continue with: [[Docker - Swarm Failure and Recovery]]