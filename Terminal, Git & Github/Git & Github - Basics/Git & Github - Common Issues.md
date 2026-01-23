---
icon: LiGithub
---


### Common Problems: 

##### Local vs Remote Histories  
Sometimes GitHub already has commits (like an auto-generated README) while your local project has its own commits.  
When you try to push, you may see:  

`![rejected] main -> main (non-fast-forward)`
`error: failed to push some refs to https://github.com/somegithub/somerepository`
![[Git & Github - Rejected Error.png]]

This means your **local and remote histories diverged**..
![[Git & Github - Separate Timelines.png]]
*Notice that the divergion happened at 'B'.. meaning that the top row is ahead by 'C' *

### If this happens then:

> [!success] Solution
> **Why this happens:** 
> Your **local branch** and the **remote branch** have different commits.  
> Git refuses to overwrite history unless you **merge them**.
>
> **First fetch and allow for unrelated histories:**  
> ```bash
> git pull origin main --allow-unrelated-histories
> ```
>
> - Resolve any conflicts (e.g., in `README.md`).  
> - Stage and commit the resolution:  
>   ```bash
>   git add .
>   git commit -m "some-commit-message-here"
>   ```
> - Push again:  
>   ```bash
>   git push origin main
>   ```

---

### Now what?

> [!success]  
> **YOU DID IT!**  
> Practice creating a repo, adding files, committing, and pushing to GitHub.  
> Next step: learn **branches** and **pull requests** for teamwork!


---
### Common Issues
###  Beginner Git Problems

1. **“I committed to the wrong branch”**
    - Forgetting to create/switch to a feature branch before working.
    - Fix: use `git checkout -b` early, or `git switch -c`.
2. **“I deleted files accidentally and Git removed them”**
    - Git tracks deletes as well.
    - Fix: restore with `git checkout -- filename` or `git restore filename`.
3. **“My files are ignored / not showing up”**
    - Usually caused by `.gitignore` catching them.
4. **Merge conflicts**
    - Happens when two people change the same line in a file.
    - Fix: manually edit, then `git add` and `git commit`.
5. **“Detached HEAD state”**
    - Happens when you checkout a specific commit instead of a branch.
    - Fix: create a new branch from that commit.

###  Intermediate Git Problems

6. **“Accidentally pushed secrets”** (API keys, passwords)
    - Removing them isn’t just deleting — you may need to rewrite history (`git filter-repo`) or rotate keys.
7. **“I want to undo my last commit”**
    - `git reset --soft HEAD~1` (keep changes staged)
    - `git reset --hard HEAD~1` (discard changes)
8. **Force push mistakes**
    - Overwriting remote history by accident (`git push --force`).
    - Can wipe out teammates’ work.
9. **Cloning into the wrong folder**
    - Ending up with nested repos (`myrepo/myrepo`).
10. **“I can’t pull because I have local changes”**
	- Fix: commit or stash before pulling.

###  Remote / GitHub Problems

11. **Different local vs remote histories** (your exact case)
	- Non-fast-forward errors, diverged timelines.
12. **Forgot to set upstream branch**
	- First push fails → need `git push -u origin main`.
13. **Permission denied (publickey)**
	- SSH key not set up properly.
14. **Repository not found / 403 error**
	- Wrong URL or missing permissions.
15. **Large file rejection**
	- GitHub blocks files >100MB. Needs Git LFS or different approach.



---

---
### Git & GitHub - Documentation

> [!info] Documentation  
> - [Git Official Docs](https://git-scm.com/doc)  
> - [GitHub Docs](https://docs.github.com/en/get-started/using-git)  

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]