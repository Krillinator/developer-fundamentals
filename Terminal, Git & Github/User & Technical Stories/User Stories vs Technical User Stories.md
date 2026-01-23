---
icon: LiStickyNote
---
### What is a User Story?

> A **User Story** describes **what a user wants** and **why**.  
> It focuses on **value**, not implementation.

> **Think of it as:**  
> _“What problem are we solving for the user?”_  
> 
> ![[User & Technical Stories - User Story.png|100]]


---

### A typical User Story looks like..

1. Identify a **user**
2. Describe what they **want**
3. Explain **why it matters**
4. Keep it **non-technical**
5. Add **acceptance criteria** (how we know it’s done)

> [!tip]  
> **Rule:** If a non-developer can’t understand it, it’s probably too technical.

---

### The User Story template

> **As a** `<type of user>`  
> **I want** `<something>`  
> **So that** `<reason / value>`

> [!example]
> #### Regular User Story (example)
> **As a student**  
> **I want** to submit my assignment online  
> **So that** I don’t have to email files to my teacher

> [!example]- Example 2
> #### Regular User Story (example 2)
> **As a teacher**  
> **I want** to see which students have submitted their assignment  
> **So that** I can quickly follow up with those who haven’t

> [!example]- Example 3
> #### Regular User Story (example 3)
> **As a user**  
> **I want** to reset my password  
> **So that** I can regain access if I forget it

![[Screenshot 2026-01-23 at 11.07.02.png]]
Example: [[User & Technical Stories Example - Flow Diagram.canvas|User & Technical Stories Example - Flow Diagram]]

---

### Acceptance Criteria (User Story)

> Acceptance Criteria define **what must be true** for the story to be complete.

> [!example]
> #### Acceptance Criteria
> - The student can upload a file
> - The system confirms successful submission
> - The teacher can view the submitted assignment

---

## What is a Technical User Story?

> A **Technical User Story** describes **technical work** needed to support the system.  
> It does **not** represent direct user interaction.

> **Think of it as:**  
> _“What must exist under the hood for user stories to work?”_  
> 
> ![[User & Technical Stories - Technical Story.png|100]]

---

### A typical Technical User Story looks like..

1. Describes **system behavior**
2. Focuses on **infrastructure, security, or architecture**
3. Is written **for developers**
4. Supports one or more **user stories**
5. Has clear **technical acceptance criteria**

> [!info]  
> Technical User Stories often don’t have a visible “user” — but they are still critical.

---

### Technical User Story template

> **As a** `<system / developer>`  
> **I want** `<technical capability>`  
> **So that** `<system requirement / constraint>`

> [!example]
> #### Technical User Story (example)
> **As the system**  
> **I want** to validate uploaded file types  
> **So that** only safe and supported files are stored

> [!example]- Example 2
> #### Technical User Story (example 2)
> **As the system**  
> **I want** to hash user passwords before storing them  
> **So that** stored credentials are protected if the database is compromised

> [!example]- Example 3
> #### Technical User Story (example 3)
> **As a developer**  
> **I want** to add logging for failed login attempts  
> **So that** security issues and abuse can be detected

![[Screenshot 2026-01-23 at 11.04.34.png]]
Example: [[User & Technical Stories Example - Flow Diagram.canvas|User & Technical Stories Example - Flow Diagram]]

---

### Acceptance Criteria (Technical User Story)

> [!example]
> #### Acceptance Criteria
> - Only `.pdf` and `.docx` files are accepted
> - Invalid file types are rejected with an error
> - File validation happens before storage
> - Errors are logged for debugging

---

### How they work together

> [!success]  
> **User Stories describe _what_ users need**  
> **Technical User Stories describe _how_ the system supports it**

![[User Story vs Technical Story - Flow.png|600]]

---

### Key takeaway

> [!tip]  
> If you remove **all technical terms** and the story still makes sense → it’s a **User Story**  
> If removing technical terms breaks the meaning → it’s a **Technical User Story**

---

## **Finished**
 Back to Overview: [[index.canvas|Developer Fundamentals]]