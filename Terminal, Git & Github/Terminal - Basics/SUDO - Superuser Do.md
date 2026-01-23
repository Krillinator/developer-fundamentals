---
icon: LiKey
---

### What is `sudo`?

> `sudo` stands for **“superuser do”**.  
> It lets you run commands with **administrator (root) privileges**.
> This is mostly for Mac & Linux however, Administrator rights are equally as common on Windows. *(look at the bottom page)*
> 
> **Think of it as:**  
> 	_“I need permission to do this, please act as the system admin.”_

---

### Why it’s necessary

Some commands affect the **whole system** (like installing software, changing protected files, or starting services).

- Normal users don’t have the rights to do this.
- Using `sudo` ensures you don’t accidentally break things unless you **really mean to**.

Example:

```bash
sudo apt-get update    # Linux package update
sudo rm -rf /protected-folder
```

---

### Entering the password

When you type your password after `sudo`:

- You won’t see any `*` or dots — **it looks like nothing is happening**.
- But the characters **are being recorded in the background**.
- This is a **security feature** (so nobody can look over your shoulder and see how long your password is).

Just type it normally and press **Enter**.

---

> [!warning]  
> Use `sudo` carefully.  
> It gives you **full control** over the machine — including the power to delete critical system files.

---

>[!warning] sudo across systems
>- **macOS / Linux**  
>  - Use `sudo` to run commands as **administrator (root)**.  
>  - Example:  
>    ```bash
>    sudo apt-get update     # Linux
>    sudo nano /etc/hosts    # macOS/Linux
>    ```
>  - You’ll be asked for your password (nothing shows when typing — this is normal).  
>
>- **Windows (PowerShell)**  
>  - There is **no `sudo`**.  
>  - Instead, you must **run PowerShell as Administrator** (right-click → “Run as administrator”).  
>  - Some commands require elevated privileges, but you don’t prefix them with `sudo`.  
>
>Remember: use `sudo` (or admin mode) only when necessary.. it gives you full system power, including the ability to break things!

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]