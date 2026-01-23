---
icon: LiGithub
---
### The problem

> Sometimes a file is **gone locally**.. but still exists on **GitHub** or **GitHub Pages**.  
> This usually happens after renaming or deleting files.
> 
> **In this section you will learn:**  
> how to check what Git is actually tracking, and how to remove a file **so it disappears online as well**.

---

### Workflow Explained (problem)

1. You create and push a file to GitHub.
2. You rename or delete the file locally.
3. You push again.
4. The old file still exists online.

_This is confusing.. but very common._

---

### Why this happens

- Git tracks **files**, not folders
- Deleting or renaming locally does not always update Git automatically
- GitHub only shows what exists in the **published branch**

---

### Step by Step – Guide

> [!info]+ Prerequisites
> - Git installed locally
> - A GitHub repository
> - Basic Terminal knowledge

---

> [!example]+ Step 1
> 
> #### Create and publish a file
> 
> ```bash
> mkdir git-remove-file-demo
> cd git-remove-file-demo
> git init
> ```
> 
> ```bash
> echo "First version" > page.html
> git add .
> git commit -m "Add initial file"
> ```
> 
> Push the project to GitHub.

---

> [!example]- Step 2
> 
> #### Rename the file locally (on purpose)
> 
> Rename the file **without using Git**:
> 
> ```bash
> mv page.html index.html
> git add .
> git commit -m "Rename file locally"
> git push
> ```
> 
> Do **not** fix anything yet.

---

> [!example]- Step 3
> 
> #### Check what Git is tracking
> 
> ```bash
> git ls-files
> ```
> 
> Filter the result:
> 
> ```bash
> git ls-files | grep html
> ```
> 
> This shows what Git **still knows about**.

---

> [!example]- Step 4
> 
> #### Remove the tracked file correctly
> 
> If the old file appears in `git ls-files`, remove it:
> 
> ```bash
> git rm page.html
> git commit -m "Remove old tracked file"
> git push
> ```
> 
> The file will now disappear from GitHub.

---

### Important rule

> **If a file exists online, it exists in the branch GitHub publishes.**

Deleting a file locally is not enough.

---

### Common mistake

Renaming files like this:

```bash
mv old.html new.html
```

Instead, use:

```bash
git mv old.html new.html
```

This allows Git to understand the change correctly.

---

### Now what?

> [!success]  
> **Well done!**  
> You now know how to remove files from GitHub properly — even when they refuse to disappear.

---

### Git – Documentation

> [!info] Documentation
> 
> - [git ls-files](https://git-scm.com/docs/git-ls-files)
>     
> - [git rm](https://git-scm.com/docs/git-rm)
>     

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]