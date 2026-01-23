---
icon: LiGitPullRequest
---
### What is a Pull Request?

> A **pull request (PR)** is basically a **request to merge code** from one branch into another branch (usually into `main` or `develop`).
>	*You take changes from one branch and **combine them into another***
>
> **Think of it as:**
> 	_“Hey team, I finished a piece of work on my branch. Please review it before we add it to the main project.”_
> 	
>![[Pull Request - Overview.png|500]]

---


### A typical Pull Request looks like..

1. **You create a branch** for a feature *(or bugfix)*
2. **You push your changes** to GitHub
3. **You open a PR** from your branch → into `main`
4. **Reviewers check your code** *(comment, suggest changes, approve)*
5. When approved, **it gets merged** into the main codebase

> ![[Pull Request - Flow Diagram image.png]]

*This ensures that our **main branch stays stable**!*

>[!warning]- Feedback can lead to rejection
> Although the image showcase 'discussion of feedback' then an immediate merge. It oftentimes is rejected due to: code quality, bugs, security issues, performance etc..
> When rejected, you won't be allowed to merge.

>[!tip]
> Tools such as **GitHub Projects**, together with **issues** and **user stories**, **provide structure** and **improve communication**, leading to a more efficient and higher-quality workflow.

---


### Why it’s useful

A **Pull Request (PR)** is really just a formal **proposal**:  
> “I’d like to merge my branch into `main`. Here are the changes. Please review.”

- Keeps `main` stable (only reviewed code gets in).
- Allows **discussion** before merging.
- Keeps a **history** of why and how changes were made.
- Connects to **Issues/Project boards** so work is traceable.

![[Shaking Hands.png|300]]

---
#### Draft Pull Requests
Sometimes your code isn’t ready to merge yet, but you still want feedback.  
That’s when you can open a **Draft Pull Request**:

- Shows clearly that the work is still **in progress**.  
- The green “Merge” button is disabled until you mark it **Ready for review**.  
- Useful for **early feedback**, team visibility, and testing.  


>![[Pull Request - Github Showcase 2.png | 300]]
*Open a pull request and pick what best suits you*

--- 


### Step by Step - Guide

#### Prerequisites
* A Github Account
* A Github Repository
* Basic Git Knowledge
* A local Project that is initialized with `$ git init`

> [!example]
> #### #1 Preparing the problem
> 1. Create a new branch with `$ git branch new-branch-name-here`
> 2. Add **faulty code** to the project
> 3. `$ git add . `
> 4. `$ git commit -m "Initial Commit"`
> 5. `$ git push`
> 
> With the `bad code` pushed to the branch, we'll now perform the **Fix**!

**Here's the example of my code**

**IMG #1 - Service Logic**
>![[Code Example - part 1.png]]

**IMG #2 - Controller Endpoint w/o Validation**
>![[Code Example - part 2.png]]

> [!example]
> #### #2 Performing a Pull Request
> 1. **Navigate** to your **Github Repository** 
> 2. Within the `Main` branch there's a `Compare & Pull Request` for your information!
> ![[Pull Request - Github Showcase.png]]
> 
> 3. In this example we'll go with **an ordinary** **Pull Request** 
> 
> 4. **Fill in the Description**, click `Create pull request`
> ![[Pull Request - Description.png]]
> 
> 5. **Review, discuss and comment**
> ![[Pull Request - Adding Comment.png]]
> *In this example, i'm adding a simple comment to play pretend in a group setting where we would want to add constructive feedback and suggest changes*
> 
> 6. **Add a reply to the comment** 
> ![[Pull Request - Quote Reply.png]]
>    
> 7. **Post and Publish, now merge**
   ![[Pull Request - Merge.png]]


---

### Now what?

>[!success]
>**YOU MADE IT!** 
>Make sure you try **this out a couple of times** and **familiarize yourself** with other tools such as collaborating with **Pull Requests** and **Issues**!
>
>For example, say you open up an `issue #12`, you could in a **git commit message** link to an issue using `$ git commit -m "Fix: Security Issues in Validation (#12) "`.

### Pull Request - Documentation

> [!info]  Documentation
> Read more about Pull Requests at Github's official documentation: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]