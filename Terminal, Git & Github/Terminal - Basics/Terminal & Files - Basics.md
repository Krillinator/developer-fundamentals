---
icon: LiTerminal
---


### Working with Files in the Terminal

> This section focuses on **creating**, **editing**, **reading**, and **filtering** files  
> directly from the command line.. without leaving the terminal.
> ![[Terminal - icon png.png|100]] ![[Terminal & Files - icon.png|100]]

---

### What you’ll learn

> [!info]
> By the end of this section, you will be able to:
>
> - Create files and folders
> - Edit files using a terminal-based editor
> - View file contents
> - Filter output using `grep`

---

### Creating Files

> Files can be created directly from the terminal.

> [!example]
> Create a new file:
>
> ```bash
> touch notes.md        # Mac / Linux
> New-Item notes.md     # Windows PowerShell
> ```

> [!tip]
> If the file already exists, it will **not** be overwritten.

---

### Editing Files in the Terminal

> You don’t need a graphical editor to change files.  
> Files can be edited **directly from the command line**.

> [!example]
> Open a file using `nano`:
>
> ```bash
> nano notes.md
> ```

> [!hint]- Using nano
> - Type to edit
> - Save: `Ctrl + O` → Enter
> - Exit: `Ctrl + X`

> [!info]
> `nano` is beginner-friendly, but **not guaranteed on all systems**.
>
> If `nano` is unavailable:
>
> - **Mac / Linux:**  
>   ```bash
>   vi notes.md
>   ```
> - **Windows PowerShell:**  
>   ```powershell
>   notepad notes.md
>   ```
>
> These editors are launched **from the terminal**, even if they look different.

---

### Viewing File Contents

> Sometimes you only want to **read** a file.

> [!example]
> Show file contents:
>
> ```bash
> cat notes.md
> ```

> [!example]
> View long files (scrollable):
>
> ```bash
> less notes.md
> ```

> [!hint]- Why use `less`
> - Scroll with arrow keys
> - Quit with `q`
> - Safer for large files than `cat`

---

### Filtering Output with `grep`

> `grep` lets you **filter text output** and show only what matches.

> [!example]
> Search for text inside a file:
>
> ```bash
> grep "error" notes.md
> ```

> [!example]
> Filter command output using a pipe:
>
> ```bash
> ls | grep ".md"
> ```

> [!tip]
> Think of `grep` as:
> **“Only show me lines that contain this.”**

> [!hint]- Common options
> - Ignore case:
> ```bash
> grep -i "error" notes.md
> ```
> - Show line numbers:
> ```bash
> grep -n "error" notes.md
> ```

---

### Typical File Workflow

> [!example]
> A common workflow might look like this:
>
> ```bash
> touch notes.md
> nano notes.md
> cat notes.md
> ls | grep ".md"
> ```

> Each step builds on the previous one.

---

### Now what?

> [!success]
> You can now:
> - Work with files without leaving the terminal
> - Inspect and search text efficiently
> - Use tools that appear everywhere in development
>
> These skills are used constantly when working with **code**, **logs**, and **servers**.

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]