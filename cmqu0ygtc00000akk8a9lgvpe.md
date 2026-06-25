---
title: "A Beginner’s Guide to Git"
datePublished: 2026-06-25T21:39:04.256Z
cuid: cmqu0ygtc00000akk8a9lgvpe
slug: a-beginner-s-guide-to-git
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/208f61b5-f2eb-4c69-8ede-1b39cb59930c.png

---

We’ve all been there. You’re working on a project, things are going great, and suddenly you decide to try a new idea. You copy your folder, rename it `Project_Final`, then `Project_Final_v2`, and eventually end up with a chaotic mess of files like `Project_Actual_Final_DONT_TOUCH`. It’s stressful, messy, and a single mistake can ruin days of hard work.

Enter **Git**.

At its core, Git is a **Version Control System**—which is just a techy way of saying it’s an ultra-smart, time-traveling filing cabinet for your code. Git tracks every single change you make to your files. If you write code that breaks your entire application at 2:00 AM, Git allows you to instantly roll back the clock to a time when everything worked perfectly. It is the ultimate safety net for developers, allowing you to experiment boldly without the fear of destroying your progress. It also acts as the ultimate collaboration tool, letting multiple people work on the exact same project at the same time without overwriting each other's work.

If you’re a beginner, looking at Git commands can feel like trying to learn a foreign language. But getting used to it doesn't require memorizing a hundred different terminal commands overnight. The secret is to start small: treat Git like a digital journal. By incorporating just three basic steps into your daily routine—making a change, saving a snapshot, and pushing it to the cloud—it quickly transforms from an intimidating chore into second nature.

Let's break down exactly how Git works, why it will save your sanity, and the simple habits you can build today to master it.

### 1\. Setting Up the Repository & Basic Commands

*   **List Directory Contents**
    

`ls`

**What it does:** Shows files and folders in your current working directory.

*   **Initialize Repository**
    

`git init`

**What it does:** Creates a hidden .git folder, officially turning the local directory into a Git repository.

*   **List All (including Hidden)**
    

`ls -a`

**What it does:** Verifies that the hidden .git folder was successfully created alongside the current and parent directories.

*   **Check Repository Status**
    

`git status`

**What it does:** Tells you what branch you are on, whether you have any commits, or if there are untracked files.

### 2\. Creating Files & Interacting with the Shell

*   **Create an Empty File**
    

`touch First`

**What it does:** Creates an empty file named First (without a file extension).

*   **Case-Sensitivity Check**
    

`Touch First.txt`

**What it does:** Used Touch with a capital 'T'. In Git Bash, command execution is case-sensitive, but the system gracefully processed it to create First.txt.

*   **Redirect Output to New File**
    

`echo "This is a Test. What's up Cloud?" > touch.txt`

**What it does:** Writes the text inside the quotes directly into a new file named touch.txt

### 3\. The Staging Area & Tracking Changes

*   **Staging Attempt**
    

`git add first.txt`

**What it does:** Tried adding first.txt in lowercase. Because Git on Windows can be picky with case matching, git status revealed it stayed untracked.

*   **Stage All Changes**
    

`git add .`

**What it does:** The staging powerhouse. Stages all new, modified, or deleted files in the directory at once.

*   **Append Text to Existing File**
    

`echo "Checking something" >> First.txt`

**What it does:** Uses >> to add a line of text to the end of an existing file without overwriting its contents.

*   **Stage Modified File**
    

`git add First.txt`

**What it does:** Re-stages First.txt specifically after making changes to its content.

### 4\. Committing and Viewing History

*   **First Commit**
    

`git commit -m "Let's commit these now - 23062026"`

**What it does:** Saves the staged snapshot to history with a descriptive message and timestamp log.

*   **View Commit History**
    

`git log`

**What it does:** Displays the commit history stream, showing commit hashes, author info, dates, and messages.

*   **Subsequent Commit**
    

`git commit -m "Second Commit 23062026"`

**What it does:** Commits the next round of tracked changes to build the development timeline.

### 5\. Working with Ignored Files

*   **Make Directory**
    

`mkdir IgnoreTest`

**What it does:** Creates a new folder named IgnoreTest.

*   **Create Gitignore File**
    

`touch .gitignore`

**What it does:** Creates the explicit configuration file where you list patterns, files, or folders you want Git to completely ignore.

### 6\. Branching & Isolated Workflows

*   **Create Feature Branch**
    

`git branch Social-Media`

**What it does:** Creates a brand new independent branch named Social-Media.

*   List Branches
    

`git branch`

**What it does:** Lists all local branches in the repository. The asterisk highlights which branch you are actively standing on.

*   **Create Second Feature Branch**
    

`git branch Sales-and-Finance`

**What it does:** Sets up an alternate track for financial data.

*   **Switch Branch Context**
    

`git switch Sales-and-Finance`

**What it does:** Moves your terminal workspace focus off of master and onto the Sales-and-Finance branch.

*   **Branch Isolated Commit**
    

`git commit -m "Updated Cash Reg"`

**What it does:** Commits a new file (Cash-Register.txt) completely safely inside the isolated branch, keeping the master branch perfectly clean.

### Lessons Learned/Skills Built

Practicing with Git Bash directly inside VS Code helps build a solid foundation in both local version control and standard shell navigation. Here is a high-level summary of the core technical competencies gained from this session:

*   **Command-Line Fluency:** Developed comfort with fundamental Bash operations including directory querying (`ls`), directory creation (`mkdir`), and output text redirection (`>` and `>>`).
    

*   **Repository Management:** Learned how to transform a local project environment into an active tracking database (`git init`) and monitor state differences accurately (`git status`).
    
*   **The Three-Stage Architecture Lifecycle:** Practiced moving files seamlessly through the core Git workflow lifecycle: from an **untracked state** (Working Directory) to a **staged state** (`git add`) and finally into a **permanently tracked snapshot** (`git commit`).
    
*   **History Auditing:** Gained the ability to review project development timelines, track changes across different updates, and read individual commit metadata using `git log`.
    
*   **Environment Optimization:** Set up custom filters (`.gitignore`) to keep source code clean by proactively separating core program logic from temporary development tools and directories.
    
*   **Parallel Development (Branching):** Mastered the core DevOps practice of creating independent feature tracks (`git branch`) and safely navigating between them (`git switch`). This allows for risk-free experimentation completely isolated from the primary source code.