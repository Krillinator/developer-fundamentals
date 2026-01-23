---
icon: LiTerminal
---

![[Terminal - Workflow abstract.png|200]]

### What is the Terminal?

> The **Terminal** (sometimes called _command line_ or _shell_) is a way to **control your computer with text commands** instead of clicking.  
> 
> It lets you **navigate**, **create files/folders**, and **run programs** directly.
> 
> **Think of it as:**  
> _“Talking to your computer in its own language, step by step.”_  
> ![[Terminal - icon png.png|100]]

---

### A typical Terminal workflow looks like..

1. **Open the Terminal**
   - **Windows:** Press `Win + R`, type `powershell`, hit Enter  
   - **Mac:** Press `Command + Space`, type `Terminal`, hit Enter  
   - **Linux:** Press `Ctrl + Alt + T`  
1. **Navigate** to a folder (`cd` = change directory).  
2. **List files** (`ls` or `dir`).  
3. **Create or remove** files/folders.  
4. **Check where you are** (`pwd`).  


> ![[Terminal - Sudo info png.png|100]]

_This helps you move around and manage your project directly from text commands!_

> [!tip]  
> **Windows vs Mac/Linux**:
> 
> - `dir` (Windows) ≈ `ls` (Mac/Linux)
>     
> - Most other commands are very similar across systems.
>     

---

### Why it’s useful

The **Terminal** is your direct connection to the computer:

> “I want to manage my files and projects quickly, without clicking through folders.”

- Much faster for **development tasks**.
- Works the **same across projects** (portable knowledge).
- Often the **only way** to use tools like Git, Docker, or Node.js.
- Gives more **control and visibility** than the file explorer alone.

![[Terminal - Sudo think png.png|100]]

---

### Step by Step - Guide

#### Prerequisites

- **Windows:** Open **PowerShell** (search in Start menu).
- **Mac:** Use `COMMAND + SPACE` Search for **Terminal** app.
- **Linux:** Use `**Ctrl + Alt + T**` to open a Terminal.

> ![[Terminal - Navigate - Flow Diagram.png|900]]


> [!example]
> 
> #### #1 Navigating Folders
> 
> - Move into a folder:
>     
>     ```bash
>     cd my-folder
>     ```
>     
> - Move **one step back**:
>     
>     ```bash
>     cd ..
>     ```
>     
> - Move into your **home directory**:
>     
>     ```bash
>     cd ~
>     ```
>     
> - Show where you are:
>     
>     ```bash
>     pwd     # Mac/Linux
>     Get-Location   # Windows PowerShell
>     ```
>     

>[!hint]- Navigation tips
>1. **Go up one folder (parent):**  
>  ```bash
>  cd ..
>  ```  
>
>2. **Go back to previous location:**  
>  - Mac/Linux:  
>    ```bash
>    cd -
>    ```  
>  - Windows PowerShell:  
>    ```powershell
>    Pop-Location
>    ```

---

> [!example]- Example 2
> 
> #### #2 Viewing Files
> 
> - Show files in current folder:
>     
>     ```bash
>     ls        # Mac/Linux
>     dir       # Windows
>     ```
>     
> - Show **hidden files**:
>     
>     ```bash
>     ls -a
>     ```
>     

---

> [!example]- Example 3
> 
> #### #3 Creating Files & Folders
> 
> - Create a new file:
>     
>     ```bash
>     touch file.md   # Mac/Linux
>     New-Item file.md   # Windows PowerShell
>     ```
>     
> - Create a new folder:
>     
>     ```bash
>     mkdir test
>     ```
>     
> - Remove an empty folder:
>     
>     ```bash
>     rmdir test
>     ```
>     
> - Remove a file or folder (force):
>     
>     ```bash
>     rm -rf test     # Mac/Linux
>     Remove-Item test -Force -Recurse   # Windows PowerShell
>     ```
>     

> [!warning]-  
> Be careful with **delete commands** (`rm -rf` on Mac/Linux or `Remove-Item -Force` on Windows).  
> They **skip the recycle bin** and **permanently erase files**.

---

### Productivity Tips in the Terminal

> Working in the terminal becomes much faster once you know a few **shortcuts**.
> These small tricks help you stay efficient while navigating and managing files.

> [!hint]- Clear Text
>
>- **Mac/Linux:** `clear`
>- **Windows:** `cls`

> [!hint]- Reuse Commands
>
>- Press **↑ / ↓** to cycle through previous commands
>- (Mac/Linux only) run last command again with:
  
> [!hint]- Autocomplete
>
>- Start typing, press **Tab** → fills in the file/folder name automatically

> [!hint]-  Cancel a Running Command
>
>- Works everywhere:
>```
>Ctrl + C
> ```

---
### Now what?

> [!success]  
> **YOU DID IT!**  
> Practice **navigating**, **creating**, and **deleting** files with these commands.  
> Once you’re comfortable, you’ll be ready to use the Terminal for **Git, programming, and automation**.
> 
> Unsure what to do next? 
> Click here and go to the next step: [[index.canvas|Developer Fundamentals]]

---

### Terminal - Documentation

> [!info] Documentation
> 
> - Windows PowerShell: [https://learn.microsoft.com/en-us/powershell/](https://learn.microsoft.com/en-us/powershell/)
>     
> - Mac/Linux (Bash): [https://www.gnu.org/software/bash/manual/](https://www.gnu.org/software/bash/manual/)
>     

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]