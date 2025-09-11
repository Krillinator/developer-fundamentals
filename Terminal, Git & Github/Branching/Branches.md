
![[Git branch - Workflow abstract png.png|200]]
### What is a Branch?

> A **branch** in Git is basically a **separate line of development**.  
> _You can work on new features or fixes **without touching the main codebase**_
> 
> **Think of it as:**  
> _“I’ll create a copy of the project at this point in time and make changes there, so the `main` branch stays safe.”_  
> ![[Git Branch - What it is.png|300]]

---

### A typical Branch workflow looks like..

1. **You create a branch** for a new feature or bugfix.
2. **You make commits** only in that branch.
3. **You push it** to GitHub.
4. Later, you can **merge it back** into `main` through a Pull Request.

> ![[Terminal - Git Branch - Flow Diagram.png]]

_This ensures that experiments or changes don’t break the stable version!_

> [!tip]  
> Use **branch naming conventions** like `feature/add-login`, `bugfix/fix-validation`, or `hotfix/security-patch`.  
> This makes **collaboration** and **tracking** much **clearer**.

---

### Why it’s useful

A **branch** is really just a **safe workspace**:

> “I want to try something new without messing up `main`.”

- Keeps `main` clean and stable.
- Makes it easy to **test ideas** or **fix issues** independently.
- Supports **parallel teamwork** (multiple devs working on different features at the same time).
- Provides a **history of changes** tied to each feature or fix.

>![[Git branch - Benefits.png|500]]

#### Common Branch Types

- **`main`** → always the stable production-ready code
- **feature branches** → for new features
- **bugfix branches** → to solve bugs
- **hotfix branches** → urgent fixes on `main` *(sometimes merged with bugfix)*
- **develop branch *(optional)*** → staging ground before merging into `main`

> ![[Git Branch - Workflow.png|500]]

---

### Step by Step - Guide

#### Prerequisites

- Git installed locally
- A GitHub Repository
- Basic Git Knowledge

> [!example]  
> #### #1 Creating and Switching Branches  
> 
> 1. Create a new branch
> 	
> 	``` bash
> 	 git branch new-feature
> 	```
> 	 
> 2. Switch to it
> 	
> 	``` bash
> 	 git checkout new-feature
> 	```
> 	
> 3. Work on your code and commit changes
> 	
> 	 ``` bash
> 	 git add .
> 	 git commit -m "Add new feature"
> 	 ```
> 	
> 4. Push to GitHub
> 	
> 	 ``` bash
> 	 git push -u origin new-feature
> 	 ```
> 	

> [!hint] Create new branch & Switch: Shortcut
> `git checkout -b new-feature` creates + switches branch


> [!example]
> 
> #### #2 Viewing and Managing Branches
> 
> - Show all branches:
>     
>     ```bash
>     $ git branch  
>     ```
>     
> - Delete a local branch:
>     
>     ```bash
>     $ git branch -d branch-name  
>     ```
>     
> - Delete a remote branch:
>     
>     ```bash
>     $ git push origin --delete branch-name  
>     ```
>     

>[!warning]  
>When working in a team, creating a branch locally does **not** make it visible to others.  
>If *Person A* creates a branch called `feature/test-A` and pushes it,  
>*Person B* won’t see it until they **fetch the latest changes**.  
>
>To view all branches (both local and remote), run:  
>```bash
>git branch -a
>```


---

### Now what?

> [!success]  
> **YOU GOT IT!**  
> Play around with **creating, switching, and deleting branches** to get comfortable.  
> Combine this with **Pull Requests** and **Issues** to create a **professional Git workflow**.

### Branch - Documentation

> [!info] Documentation  
> Read more about Branches at Git’s official documentation: [https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)
