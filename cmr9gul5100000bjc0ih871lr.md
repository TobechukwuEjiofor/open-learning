---
title: "Git 102 🤓"
datePublished: 2026-07-06T17:00:29.739Z
cuid: cmr9gul5100000bjc0ih871lr
slug: git-102
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/8f955d8b-7bda-4360-813a-dee34b8ceb55.jpg

---

So far on my cloud and DevOps learning journey, I've mainly been deep in Azure territory: deploying VMs, configuring NSGs, building VM Scale Sets. But I have gradually started to enter the DevOps world, and today I’m navigating deeper into something that “underpins almost everything in DevOps”: **Git**.

I didn't just read about it. I opened my terminal, made mistakes, fixed them, and kept going. Below is an honest account of what that looked like.

## Starting Simple

I already had VS Code open from my earlier git experiments. The first thing I did was navigate into a working folder and initialise a repository. Nothing dramatic: just `git init`, create a few files, and start building the habit of staging and committing.

```plaintext
git add .
```

```plaintext
git commit -m "first commit"
```

Simple enough. But these things have a way of humbling you the moment you get comfortable.

## The Vim Trap

The first real "what just happened" moment came when I ran a `git merge` and my entire terminal transformed into something that looked like a broken screen. Text everywhere, no cursor I could find, and the word **insert** blinking at the bottom.

I had accidentally walked into **Vim**, the terminal-based text editor that Git opens by default when it needs you to write a commit message. It was genuinely disorienting. You can't just type and exit. You can't click anywhere. Even closing the window feels like the wrong move.

The fix, once I AI’d it, was simple:

*   Press **Esc** to exit insert mode
    
*   Type `:wq` and hit Enter to save and quit
    

But getting there required stepping back, understanding what had happened, and not panicking. The lesson is that **Git's default text editor is Vim, and Vim has its own rules.** To avoid this entirely in future, I configured VS Code as my default editor instead:

```plaintext
git config --global core.editor "code --wait"
```

Now when Git needs a message, it opens a familiar VS Code tab. Problem permanently solved (for me).

## Branching, and a Naming Mistake

Once I was comfortable with the basic commit workflow, I moved on to branching. This is where Git starts to feel genuinely powerful: the ability to spin off a parallel line of work without touching your “main branch”.

I created a branch called **CX**, switched to it, made some changes, and committed them. Then I switched back to main and merged CX in. Clean, straightforward.

But somewhere along the way I also ended up with a branch called **switch**, because I had typed `git branch switch Dev` instead of `git switch Dev`. Git read that as an instruction to *create a branch named "switch"* rather than switch to the Dev branch. Two completely different commands that look almost identical.

```plaintext
git branch switch Dev    # WRONG — creates a branch called "switch"
```

```plaintext
git switch Dev           # CORRECT — switches to the Dev branch
```

I deleted the accidental branch immediately:

```plaintext
git branch -d switch
```

Small mistake, good lesson. **Read your commands carefully, a missing hyphen or swapped word order can do something completely different from what you intended.**

## Understanding What Branching Actually Means

One thing that confused me was why, after creating a new branch, I could still see all the files from the main branch. I was expecting the branch to feel like a clean slate.

The reality is more nuanced and actually more elegant: **a branch is not a separate empty folder. It's a parallel timeline of the same project.** When you create a branch, it starts as an exact copy of wherever you branched from. The separation only becomes visible when you start making changes such as edits, new files, deletions, and then switch between branches.

I tested this by creating `brand-new.txt` on the CX branch, committing it, then switching back to main. The file disappeared. Switched back to CX, it reappeared. That moment of seeing files appear and vanish based on which branch you're on is when branching genuinely clicks.

## Git Diff - Seeing Your Changes Before You Commit

Another command I got hands-on with today was `git diff`. Before committing, I wanted to see exactly what had changed line by line, not just which files were modified, but the precise content difference.

```plaintext
git diff
```

The output shows removed lines in red with a `-` prefix and added lines in green with a `+` prefix. It's a habit worth building early, reviewing your diff before staging means you never accidentally commit debug code, test data, or a file you didn't mean to touch.

## Cleaning Up - Deleting and Renaming Files the Git Way

Towards the end of my session, I practised two commands that are easy to get wrong if you're used to just deleting files manually in your file explorer.

**Deleting a file:**

```plaintext
git rm File1.txt
```

This removes the file *and* stages that removal in one step. If you delete a file manually without using `git rm`, Git will notice the file is missing but won't automatically stage the deletion — you'd still need to `git add` to record it.

**Renaming a file:**

```plaintext
git mv brand-new.txt newagain.txt
```

Same principle, `git mv` handles the rename and stages it together. Git even confirms this cleanly in the commit output: `rename brand-new.txt => newagain.txt (100%)`.

## The Line Ending Warning

One last thing worth mentioning: throughout the session, Git repeatedly showed this warning:

*"LF will be replaced by CRLF the next time Git touches it"*

This isn't an error. It's Git flagging a difference between how Windows and Linux handle line endings in text files. Since I'm on Windows but my code will eventually run on Linux servers (as most Azure infrastructure does), this matters. The fix is a one-time configuration:

```plaintext
git config --global core.autocrlf true
```

Done. Git handles the conversion automatically from that point forward and the warnings stop.

## What I'm Taking Away

Today wasn't about mastering Git, it was about getting comfortable with the fundamentals through actual use, not just reading. A few honest takeaways:

*   **Mistakes are part of the process.** The accidental `switch` branch and the Vim trap were both fixed in under two minutes once I understood what had happened.
    
*   **The mental model matters more than memorising commands.** Once I understood that a branch is a parallel timeline and staging is a deliberate checkpoint, the commands started making sense rather than feeling arbitrary.
    
*   **Git is infrastructure.** Everything I'm building in Azure: VMs, scale sets, pipelines will eventually live in a Git repository. Learning this now, alongside the cloud work, is exactly the right time.
    

The journey continues. *See you in the next one.*