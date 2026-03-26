---
title: "Daily Stand-up: "We have 5 new hires starting at noon." 🕒"
datePublished: 2026-03-26T21:33:23.737Z
cuid: cmn7zonfb02b12dmmapfu2c1d
slug: daily-stand-up-we-have-5-new-hires-starting-at-noon
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/0f00b36f-4042-46be-8ae8-c1f90c416674.jpg

---

Today, I’m putting on a pretend hat. I’m an IT/Onboarding Specialist at a startup that just moved to the cloud. At our 8:00 am standup, I got some news: we just hired 5 new employees!

The catch? They are already on their way to the office and expect to start work by noon. I only have a few hours to get them set up, but this "IT Cloud Support Officer" is up to the task. Here is how I got it done using Azure Entra ID.

**Step 1: Accessing Entra ID**

First, I opened my Azure dashboard. From there, I clicked on **Microsoft Entra ID** (this is where we manage our users).

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/cfc88cd7-dbcf-470d-8c40-04e991110ef6.png align="center")

**Step 2: Finding Bulk Operations**

Next, I clicked **Add** at the top and hovered over **Create New User**. I realized that wasn't quite what I needed for a group of people, so I went back and clicked on the **User Count**. This opened up the full list of our current users.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a7be831e-c84d-431b-9785-2ddc456a18a0.png align="center")

At the top of this new page, I saw **Bulk Operations** and selected **Bulk Create**.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/10fe55e3-5735-4ad2-9849-b0b0cd3af48a.png align="center")

**Step 3: Downloading the Template**

A side menu opened up on the right. I clicked **Download** to get the CSV template. This is basically a spreadsheet that tells Azure exactly who we want to add, and some of their info.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/6d88698e-bc5d-4131-88ee-64e30ff3fc76.png align="center")

**Step 4: Filling Out the CSV (The Important Details)**

When you open the template, there is an example in the first row to show you what to do. Here are a few things I noted while filling it in:

*   **Name:** Use full names (First and Last name).
    
*   **User Name (Email):** Usually, companies use firstname.lastname, but I decided to try firstname.lastnameinitial (e.g., *tobechukwu.e*).
    
*   **Domain:** Remember that the second part of the email must be your primary domain (the part after the @ sign).
    
*   **Passwords:** They must be at least 6 characters and include a capital letter, a small letter, and numbers. I added a symbol just to be safe!
    
*   **Job Roles:** I filled these in now, but I left the other columns blank. The staff can update those once they resume work. 😊
    

**Crucial Step:** I made sure to **delete the example row** before saving the file. If you leave it there, the upload might fail.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/fdcc43fb-61f2-4177-a7a2-71226c0dcd46.png align="center")

**Step 5: The Upload**

After saving my template, I went back to the Azure portal. I clicked the **blue folder icon** to find and upload my saved sheet.

Once the file was attached, I hit **Submit**.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/d11e1c44-a43e-48ef-8503-cdaa8433dd95.png align="center")

**Step 6: Success & Refresh**

I soon received a notification that said **"Successful."** I went back to the main User page, hit refresh, and all new users were right there on the list!

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/4afedb60-3e0d-4fc3-8dad-384e7e116f58.png align="center")

**Learnings**

I was able to get everyone’s profiles ready before they even walked through the door. I also noticed that you can **Bulk Delete** users in almost the same way, which is good to know for the future.

This exercise really helped me better understand the Azure portal, identity logic, domain alignment, and security protocols under (pretend) pressure.

See you next time!