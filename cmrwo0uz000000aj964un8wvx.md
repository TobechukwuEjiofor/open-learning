---
title: "My First Hands-on with Terraform 🌍"
datePublished: 2026-07-22T22:40:01.788Z
cuid: cmrwo0uz000000aj964un8wvx
slug: my-first-hands-on-with-terraform
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/f13d7fce-79c0-4dbd-ab29-01eb6d8add16.png

---

If you’ve spent only five minutes researching DevOps or Cloud Engineering, you must’ve heard the term **Infrastructure as Code (IaC)**. And right at the center of that conversation is **Terraform**.

For months, during my ongoing training, I’ve been told I would stop building via the portal. *“Stop clicking buttons in the cloud console!” “Write your infrastructure like software!”*

As an inquisitive learner, I couldn’t wait to get my hands dirty. I wanted to feel the thrill of writing a few lines of code, hitting enter, and watching actual cloud resources spring to life.

So finally, I sat down, opened my VS Code terminal, and took my very first steps with Terraform on Azure.

Here is the story of how my first session went, the exact commands I ran, and how they translate to the real world.

**Setting the Stage: Logging In and Finding My Bearings**

Before you can build anything in the cloud, you have to tell the cloud who you are. I opened up Git Bash, navigated to my working directory, and ran my first check:

`pwd`

This `pwd` (Print Working Directory) is a simple but essential reality check. In DevOps, running commands in the wrong folder is an easy way to cause chaos. I needed to be absolutely sure I was inside my created `terraform1` directory.

Once I knew I was in the right place, it was time to introduce myself to Microsoft Azure:

`az login`

This opened a browser window, let me select my Azure account, and linked my local terminal to my active subscription.

*   In a professional setting, this is where you establish your identity. Whether you are using interactive logins like this or configuring secure Service Principals in a CI/CD pipeline, establishing a secure connection to your cloud provider should always be Step 1.
    

**Step 2: The Handshake (**`terraform init`**)**

With my Azure connection live, I was ready to let Terraform do its magic. But first, Terraform needed to prepare itself. I ran: 

`terraform init`

The terminal sprang to life, downloading the specific Azure provider plugins (`hashicorp/azurerm` v4.81.0) and creating a dependency lock file.

*   Think of this as setting up your kitchen before cooking. Terraform looks at your code, figures out which cloud (Azure, AWS, GCP) you want to talk to, and downloads the exact tools (providers) required to make those API calls.
    

**Plot Twist: The "Unsaved File" Panic 🤦‍♂️**

With initialization out of the way, I configured my provider block in [`main.tf`](http://main.tf), ready to see my blueprint. I eagerly typed out `terraform plan` and hit enter.

Instead of the beautiful plan, my terminal screen started returning red error text. It stalled, complained about provider registrations, and I ended up having to hit `Ctrl + C` to desperately interrupt the process before things went completely sideways:

`Stopping operation...`

`Interrupt received. Gracefully shutting down...`

`Planning failed. Terraform encountered an error...`

I looked at my screen in confusion. *I literally just wrote the code to fix this, why is it failing?*

Then it hit me. I looked up at the tab in my code editor. There it was, that tiny, mocking white dot. **I hadn't saved my file.**

I had written the correct configuration, but because I hadn’t hit `Ctrl + S`, Terraform was still trying to read the old, unsaved draft of my code.

**Lesson:** Your terminal doesn't care how beautiful your code looks on your screen; it only cares about what is actually written to the disk. **Always, always hit save before you run a command.**

Turn on auto-save if you have to! It will save you hours of phantom troubleshooting.

**Step 3: The Blueprint (**`terraform plan` **— Take Two!)**

After tapping `Ctrl + S` desperately, I ran the plan command again:

`terraform plan`

This time, the output was beautiful. The terminal smoothly processed the saved file and displayed a clean execution plan indicating: `Plan: 1 to add, 0 to change, 0 to destroy.`

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/839a3bba-06a4-4425-960f-1de30cd4413b.png align="center")

*   I learnt this: In production, **never** skip this step. `terraform plan` is your safety net. It allows you to review exactly what changes are about to happen before spending a single dime or risking downtime. It’s the ultimate "measure twice, cut once" tool for cloud engineers.
    

**Step 4: Bringing It to Life (**`terraform apply`**)**

It was go-time. I typed the magic words:

**terraform apply**

Terraform showed me the plan one more time and asked for confirmation. I typed `yes`, held my breath, and watched the terminal count the seconds.

 `35 seconds` later, it was official:

 ***Apply complete! Resources: 1 added, 0 changed, 0 destroyed.***

I hopped onto my Azure Portal, refreshed the page, and there it was, a perfectly provisioned Resource Group, created without me clicking a single button in the portal. The feeling of seeing your code translate into real-world cloud infrastructure is incredibly satisfying.

*   This is how tech companies deploy thousands of servers in minutes. Instead of manually clicking through menus (which could lead to human error and inconsistent environments), you run a command, and your environment is built identically, every single time.
    

 **Step 5: Leaving No Trace Behind (**`terraform destroy`**)**

As a learner, cloud costs are always in the back of my mind. The last thing I wanted was a rogue resource racking up a bill overnight. Once I celebrated my small victory, I ran:

`terraform destroy`

I typed `yes`, and in `24 seconds`, the resource group was completely torn down and wiped clean from my Azure account.

**What’s Next?**

My first hands-on session with Terraform was an absolute blast (and a great lesson in the power of `Ctrl + S`). Moving from the portal to writing code feels like unlocking a superpower. I’m excited, and this is just the beginning of my IaC journey.