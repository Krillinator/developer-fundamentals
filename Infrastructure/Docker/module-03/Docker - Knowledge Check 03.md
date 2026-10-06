---
icon: ☑
---
# Quiz

![[quiz-test-img.jpg|361]]

Before moving on, test what you've learned about **Docker Swarm, nodes, services, desired state, scaling and failure recovery**.

---


### 1. You create a Swarm with `node-one` as manager and `node-two` and `node-three` as workers. What best describes the manager's role?

- [ ] It decides where service tasks should run and works to keep the actual state consistent with the desired state
- [ ] It runs the service containers while workers monitor them and report failures
- [ ] It distributes Docker images between workers but leaves task placement decisions to each worker
- [ ] It monitors the cluster, but each worker decides which service tasks it should create locally


### 2. What best describes the relationship between managers and workers in Docker Swarm?

- [ ] Managers schedule tasks, while workers run assigned tasks and report their state back to managers
- [ ] Managers define the desired state, while workers independently decide how many tasks are needed to satisfy it
- [ ] Managers monitor worker health, while workers schedule service tasks between themselves
- [ ] Managers and workers perform the same orchestration responsibilities, but managers can additionally create services


### 3. You create `nginx-web` with 3 replicas. Which statement best describes what Swarm is being asked to maintain?

- [ ] Three NGINX tasks must be running, with exactly one task placed on each of three different nodes
- [ ] Three NGINX tasks should be running somewhere on eligible nodes in the Swarm
- [ ] Every worker should run three NGINX tasks, regardless of how many workers exist
- [ ] Three NGINX containers are created initially, but Swarm doesn't maintain that number after deployment


### 4. If a Swarm has 4 available nodes and a service has 4 replicas, Swarm guarantees that exactly one replica will run on each node.

- [ ] True
- [ ] False


### 5. `nginx-web` has a desired state of 4 replicas. A worker containing one of those tasks suddenly fails. What best describes the reconciliation process?

- [ ] The manager keeps the desired state at 4 and schedules a replacement task on an eligible node
- [ ] The manager changes the desired state to 3 until the failed worker becomes available again
- [ ] The existing task is transferred from the failed worker to another node and continues running there
- [ ] One of the remaining workers notices the missing task and creates a replacement without involving the manager


### 6. A worker fails and its task is replaced on another node. When the failed worker rejoins, Swarm will automatically move the replacement task back to it.

- [ ] True
- [ ] False


### 7. `nginx-web` currently has 3 running replicas. You update the service to 4 replicas. What happens conceptually?

- [ ] The manager compares the new desired state of 4 with the actual state of 3 and schedules one additional task
- [ ] Swarm recreates all three existing tasks together with a fourth task because the service definition changed
- [ ] The workers compare their local container counts and one worker independently creates another replica
- [ ] Swarm waits until a fourth worker exists because each service replica requires its own node


### 8. `nginx-web` has 4 replicas running `nginx:latest`. You update the service to use `nginx:1.28`. What best describes a rolling update?

- [ ] Swarm modifies the image used by each existing running container without replacing its task
- [ ] Swarm works toward the new desired image by replacing existing tasks with tasks that use `nginx:1.28`
- [ ] Swarm keeps the existing tasks unchanged and applies `nginx:1.28` only when a task eventually fails
- [ ] Swarm creates four additional `nginx:1.28` replicas and permanently keeps the original four as fallback replicas


### 9. Adding a new worker node to a Swarm automatically increases the number of replicas for an existing service.

- [ ] True
- [ ] False


### 10. Your Swarm currently has one manager. You add `node-five` using the manager join token, giving the Swarm two managers. Which statement best describes the result?

- [ ] The two managers can tolerate either manager failing because the remaining manager still represents half of the manager group
- [ ] Both managers become leaders and independently coordinate different parts of the Swarm
- [ ] One manager can act as Leader and the other as Reachable, but two managers still require both for a majority
- [ ] The original manager remains responsible for orchestration while the second manager acts only as a backup and doesn't participate in consensus


### 11. What operational model does Docker Swarm use for managing a cluster?

- [ ] Declarative
- [ ] Imperative
- [ ] Closed-source
- [ ] Nonexistent

### 12. What effect does the routing mesh have on a Docker Swarm cluster?

- [ ] Commands such as `docker service create` sent to any node are automatically routed to a manager
- [ ] Requests sent to a published port on any Swarm node can be routed to a node running a task for that service
- [ ] Every service automatically runs at least one container on every node in the cluster
- [ ] The routing mesh provides Layer 7 load balancing for services


### 13. The `docker swarm init` command generates a join token. What is the purpose of that token?

- [ ] It allows you to remotely control production applications
- [ ] It initializes a Swarm
- [ ] It helps prevent unauthorized nodes from joining the Swarm
- [ ] It outputs the nodes currently participating in the Swarm


### 14. The more manager nodes you have, the easier it is to achieve consensus on the state of a cluster.

- [ ] True
- [ ] False

---

**See how you did below.**

> [!SUCCESS]- Quiz Answers
> **1. It decides where service tasks should run and works to keep the actual state consistent with the desired state**
>
> The manager coordinates the Swarm. It schedules tasks and compares the state of the cluster with the state described by our services.
>
> ---
>
> **2. Managers schedule tasks, while workers run assigned tasks and report their state back to managers**
>
> Workers execute the work assigned to them and report task state. Managers use that information when coordinating the cluster and maintaining its desired state.
>
> Remember that a **manager can also run service tasks by default**. Manager and worker describe responsibilities within the Swarm, not whether a machine is physically capable of running containers.
>
> ---
>
> **3. Three NGINX tasks should be running somewhere on eligible nodes in the Swarm**
>
> `3 replicas` describes how many instances of the service Swarm should maintain. It doesn't specify that they must be placed on three different machines.
>
> ---
>
> **4. False**
>
> The number of replicas does not determine how they are distributed across nodes.
>
> Swarm will maintain **4 running tasks**, but multiple tasks may be scheduled on the same eligible node.
>
> ```text
> 4 nodes + 4 replicas ≠ automatically one replica per node
> ```
>
> ---
>
> **5. The manager keeps the desired state at 4 and schedules a replacement task on an eligible node**
>
> The failure changed the **actual state**, not the desired state.
>
> ```text
> Desired: 4
> Actual:  3
> ```
>
> The manager detects the difference and schedules a replacement task. It doesn't repair or move the old container.
>
> ---
>
> **6. False**
>
> If the replacement task is already running, the desired state has been restored.
>
> ```text
> Desired: 4
> Actual:  4
> ```
>
> Swarm doesn't automatically move healthy tasks back to a returning worker simply to rebalance the cluster.
>
> ---
>
> **7. The manager compares the new desired state of 4 with the actual state of 3 and schedules one additional task**
>
> This time nothing failed. **We deliberately changed the desired state.**
>
> ```text
> Desired: 3 → 4
> Actual:       3
> ```
>
> One additional task is therefore required.
>
> ---
>
> **8. Swarm works toward the new desired image by replacing existing tasks with tasks that use `nginx:1.28`**
>
> A rolling update doesn't modify the image inside an existing container. Swarm replaces tasks so that the service gradually moves toward its new desired configuration.
>
> This is another example of desired state:
>
> ```text
> Old desired image: nginx:latest
> New desired image: nginx:1.28
> ```
>
> ---
>
> **9. False**
>
> Adding `node-four` changes the **resources available to the Swarm**, but it doesn't change the desired replica count of an existing service.
>
> If the service is configured for 3 replicas, Swarm will continue maintaining 3 replicas until we explicitly change its desired state.
>
> ---
>
> **10. One manager can act as Leader and the other as Reachable, but two managers still require both for a majority**
>
> Managers participate in **Raft consensus**, which requires a majority.
>
> With two managers:
>
> ```text
> Managers:          2
> Majority required: 2
> ```
>
> Losing either manager means the remaining manager no longer has a majority.
>
> With three managers:
>
> ```text
> Managers:          3
> Majority required: 2
> ```
>
> One manager can then become unavailable while the remaining two still form a majority.
>
> ---
>
> **11. Declarative**
>
> Docker Swarm uses a **declarative operational model**.
>
> Instead of telling Docker exactly how to perform every individual action, we declare the **desired state** of the system.
>
> For example:
>
> ```text
> Desired: 4 replicas
> Actual:  3 replicas
> ```
>
> Swarm determines what actions are necessary to make the actual state match the desired state.
>
> This is why **desired state and reconciliation** are examples of declarative management.
>
> ---
>
> **12. Requests sent to a published port on any Swarm node can be routed to a node running a task for that service**
>
> Docker Swarm's **routing mesh** allows a published service port to be reached through any node in the Swarm, even if that particular node isn't running one of the service's tasks.
>
> For example, a request could enter through `node-one`, while the NGINX task that handles the connection is running on `node-three`.
>
> ```text
> Request → node-one:80 → routing mesh → NGINX task on node-three
> ```
>
> The routing mesh works at **Layer 4**, routing TCP or UDP connections. It is not a Layer 7 HTTP-aware load balancer.
>
> ---
>
> **13. It helps prevent unauthorized nodes from joining the Swarm**
>
> A **join token acts as a secret credential** that a node must provide when joining an existing Swarm.
>
> When we added new nodes, we retrieved either:
>
> ```bash
> docker swarm join-token worker
> ```
>
> or:
>
> ```bash
> docker swarm join-token manager
> ```
>
> Worker and manager tokens are different because they allow a node to join the Swarm with different roles.
>
> Join tokens should therefore be treated as **secrets** and shouldn't be committed to Git or shared publicly.
>
> ---
>
> **14. False**
>
> Docker Swarm managers use **Raft consensus**, which requires a majority of managers to agree.
>
> ```text
> Managers    Majority required
> 1           1
> 3           2
> 5           3
> 7           4
> ```
>
> More managers can provide greater fault tolerance, but they don't make consensus easier. More managers also means more managers must participate in reaching a majority.
>
> This is why an **odd number of managers**, commonly 3 or 5, is generally preferred.

**Passing grade: 80% — 12/14 correct**

**Congratulations!** 
Whenever you're ready, check out Kubernetes: [[Kubernetes - Start Here]]