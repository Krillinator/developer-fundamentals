---
icon: LiGithub
---

### What is Git & GitHub?

> **Git** is a version control system.. it lets you **track changes** in your code over time.  
> **GitHub** is an online platform where you can **store Git repositories**, collaborate, and share your projects.  
>
> **Think of it as:**  
> 	_“Git saves your project’s history, GitHub makes it easy to share that history with others.”_  
> 	
> ![[Git & Github - Git + Github png.png|300]]

---

### A typical Git workflow looks like..

1. **Initialize** a Git repository in your project (`git init`).  
2. **Add** files you want Git to track (`git add`).  
3. **Commit** changes with a message (`git commit`).  
4. **Push** your commits to GitHub (`git push`).  

> ![[Git & Github - Workflow.png|500]]

_This ensures your code history is saved locally and synced to GitHub._

---

### Why it’s useful

With Git & GitHub you can:  

- Keep a **history of changes** (never lose work).  
- **Collaborate** with teammates (branches, pull requests).  
- Roll back to an earlier version if something breaks.  
- Work safely on new features without touching stable code.  

![[Git & Github - Benefits diagram.png]]

---

### Step by Step - Guide

#### Prerequisites

- [Install Git here](https://git-scm.com/downloads) (`git --version` <-- typing this in the Terminal will show it as installed)
	- *If this doesn't work, make sure you restart the computer!*
- A GitHub account  
- A project folder ready  
- Github Tokens setup
- Basic Terminal knowledge

![[Git & Github - First Project Workflow.png]]

---
### First: Create a Repository on GitHub

Before we publish anything, we need a repository (repo) on GitHub.. this will be the “home” for your project.

> [!example] Step 0 
> 
> **Create Repository**
> 1. Go to [GitHub](https://github.com)  
> 2. In the top-right corner, click the **+**, **Create New** and choose **New repository**  
>    ![[Git & Github - Repository Creation.png]]
> 3. Name your repo *(e.g. `my-first-repo`)*  
> 4. Choose visibility:  
>    - **Public** *(anyone can see it)*  
>    - **Private** *(only you and collaborators)*  
> 5. **Important:** Leave “Add a README” unchecked *(we want an empty repo to push to)*  
> 6. Click **Create repository**  



---

### Preparing a Local Project

Now let’s make something simple that we’ll upload to GitHub.

> [!example] Step 1
> **Create Project Folder**
> Create an empty folder for the project and add a file to track.
>
> **Mac / Linux (Terminal):**
> ```bash
> mkdir my-first-repo
> cd my-first-repo
> echo "This is my backup." > my-backup.txt
> ls
> ```
>
> **Windows (PowerShell):**
> ```powershell
> mkdir my-first-repo
> cd my-first-repo
> echo "This is my backup." > my-backup.txt
> dir
> ```
>
> You should now see a file called `my-backup.txt` in the folder.

---

### Publishing Our Local Project to GitHub

>[!warning] Before we begin..
>
>**Navigate to your project within the terminal**
>Always make sure you’re in your project’s **root directory** before running Git commands.  
>If you’re in the wrong place, you might accidentally publish sensitive files.  
>
>Check your current location with:  
> ```bash
> pwd
> ```
>
>If you accidentally initialized a Git repository and want to remove it, see the bottom section for more information.

---

> [!example]+ Step 2  
> #### Initialize a Git Repository
> 
> Inside your project folder:  
> ```bash
> git init
> ```
> 
> This creates a hidden `.git` folder where Git tracks your project’s history.
> 
> **Verify**
> type `ls -a` to verify the existence of the `.git` folder

>[!tip]- Git Status
>At any step, you can run `git status` to see the current state of your repository.  
>This shows which files are **staged**, **unstaged**, or if there’s anything **left to commit**.

---

> [!example]- Step 3  
> #### Add Files
> 
> Add all files in your project:  
> ```bash
> git add .
> ```
> Or add a specific file:  
> ```bash
> git add my-backup.txt
> ```

---

> [!example]- Step 4  
> #### Commit Changes
> 
> Save your changes with a message:  
> ```bash
> git commit -m "Initial commit"
> ```
> 
> Add a **title + description**:  
> ```bash
> git commit -m "Fix: login validation" -m "Added null-check and proper error handling"
> ```

---

> [!example]- Step 5  
> #### Push to GitHub
> 
> First, connect your local repo to GitHub:  
> ```bash
> git remote add origin https://github.com/your-username/my-first-repo.git
> ```
> 
> Then push your code:  
> ```bash
> git push -u origin main
> ```

>[!tip]- After the Initial Setup
>That first push with `git push -u origin main` also sets up the connection,  
>so in the future you only need:
>
>- `git add .`  
>- `git commit -m "Your message"`  
>- `git push`  
>
>**From now on, your workflow is much simpler.**

---
### Removing Unwanted Initialized Git Repositories

>[!warning] Accidental git init
>If you accidentally **initialized a Git repository** and want to remove it,  
>delete the hidden `.git` folder:  
>```bash
>rm -rf .git
>```  
>*This completely removes Git tracking from the project.* 
>This will remove all local commits therefore it is recommended to perform a project backup!
---


---

### Now what?

> [!success]  
> **YOU DID IT!**  
> Practice creating a repo, adding files, committing, and pushing to GitHub.  
> Next step: learn **branches** and **pull requests** for teamwork!

---

### Git & GitHub - Documentation

> [!info] Documentation  
> - [Git Official Docs](https://git-scm.com/doc)  
> - [GitHub Docs](https://docs.github.com/en/get-started/using-git)  

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]