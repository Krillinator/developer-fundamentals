

### What is a GitHub Token?

> A **Personal Access Token (PAT)** is a secure alternative to your password when pushing or pulling code from GitHub.  
> Since August 2021, GitHub no longer allows normal account passwords for Git operations.  
>
> **Think of it as:**  
> 	_“Your personal access key that Git uses instead of your GitHub password.”_  
>  
> ![[Git & Github - Token Password.png|200]]

---

### A typical Token workflow looks like..

1. **Generate** a Personal Access Token in GitHub.  
2. **Store** it securely (password manager or note).  
3. **Use** it instead of your password when Git asks for credentials.  

_This ensures secure access between your local Git client and GitHub._

---

### Why it’s useful

- 🔐 **More secure** than passwords  
- 🔑 Required for all GitHub operations over HTTPS  
- 🎯 Lets you control exactly what the token can do (scopes/permissions)  
- 🕒 Tokens can expire — keeping your account safer  

---

### Step by Step - Guide

#### Prerequisites
- A GitHub account  
- Git installed locally  
- Basic Terminal knowledge  

---

> [!example]+ Step 1  
> #### Navigate to Token Settings
> 
> 1. Go to [GitHub Settings](https://github.com/settings/profile)  
> 2. Select **Developer settings** → **Personal access tokens** → **Tokens (classic)**  
> 3. Click **Generate new token**  

---

> [!example]- Step 2  
> #### Configure Token
> 
> - Give your token a name (e.g. *“Laptop-Git”*)  
> - Set an **expiration date**  
> - Choose scopes (permissions):  
>   - `repo` (full control of private/public repos)  
>   - `workflow` if you’ll use GitHub Actions  
> - Generate the token  

---

> [!example]- Step 3  
> #### Save the Token
> 
> - Copy the token and **store it safely** (password manager, notepad, etc.)  
> - ⚠️ GitHub will **not show it again** after you close the page!  
> 	- If lost, you must generate a new one.  

---

> [!example]- Step 4  
> #### Use the Token
> 
> Next time you push or pull, Git will ask for credentials (we'll talk more about what this means in future tutorials):  
> - **Username:** your GitHub username  
> - **Password:** paste your token  
> 

>[!tip]- Credential Manager
> To avoid pasting the token every time:  
> - On Mac/Linux → use `git-credential-manager`  
> - On Windows → Git automatically uses the Windows Credential Manager  

---

### Common Problems

1. **“Authentication failed”**  
   - Token expired → generate a new one.  
   - Wrong scopes → recreate token with `repo` access.  
2. **Lost your token?**  
   - You cannot recover it. Delete the old one and generate a new token.  
3. **Multiple tokens**  
   - It’s okay to have one per device (Laptop, Work PC, etc.).  

---

### Now what?

> [!success]  
> **YOU DID IT!**  
> You now have secure access to GitHub from your computer.  
> Next step: push your first project confidently using your token!

---

### GitHub Tokens - Documentation

> [!info] Documentation  
> - [Managing your personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)  
> - [Git Credential Manager](https://github.com/GitCredentialManager/git-credential-manager)  
