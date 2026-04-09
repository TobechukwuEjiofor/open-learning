---
title: "Azure Quest: Mapping the Cloud with VNets and Subnets 🗺️"
datePublished: 2026-04-09T14:01:00.000Z
cuid: cmnrjozu6000021cphhfk02i4
slug: azure-quest-mapping-the-cloud-with-vnets-and-subnets
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/9dcca0bd-dd98-46ef-b5b8-e7cf63a4f5cb.png
tags: azure, networking, azure-functions, azure-devops, learning-in-public, vnet

---

After moving from user management to hosting websites, I have now reached the "backbone" of the cloud: **Networking.**

Before I started clicking, I had to get my head around what it was truly about.

Here is how I see it:

**• VNet (Virtual Network):** Think of this as your organization's private island in the Azure ocean. It’s a dedicated space where your resources (like VMs or Databases) can talk to each other securely without the whole world listening in.

**• Subnet:** This is how you divide your island into smaller towns. You might have one town for your "Frontend" (the public part) and another for your "Database" (the private part). Subnets let you set up security doors between these areas so only authorized traffic can pass through.

Here is how I set up my first cloud network *step-by-step*:

**Step 1: Finding the Foundation**

From my Azure homepage, I used the search bar. I typed in "Virtual Ne..." and it popped up immediately. (Pro tip: searching for "VNET" works just as well!). Once it appeared, I clicked in.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a2501784-46d9-4f93-a900-8240d87f2162.png align="center")

**Step 2: Starting the Build**

It opened the Network Foundation page. Since I didn’t have any networks yet, I clicked Create right in the center.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/b42bb285-293e-401f-9c7c-ca6565d0b104.png align="center")

**Step 3: Basics & Naming**

This page is where the VNet gets its identity. I had to:

• Select my Subscription.

• Choose a Resource Group (the "folder" this network sits in).

• Give it a name and pick a Region (the physical location of the servers).

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/36324bab-b34b-41a2-8c24-5368b263b6a5.png align="center")

**Step 4: The Security Layer**

Next was the Security page. It was interesting to see options for Firewalls and Encryption. Since I’m just testing and learning, I left these unchecked for now, but it’s good to know exactly where those security doors live.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/916bd31e-4a9f-4c5f-9a85-8a1ed0c46aeb.png align="center")

**Step 5: Defining the Space (IP Addresses)**

This is where some math comes in! Azure asks how much space you need. You'll see a range from /2 to /29.

• I selected 10.0.0.0/24.

• Learning Moment: The /24 "locks" the first part of the address and gives me 256 IP addresses to play with.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/00fe725f-b709-4ab4-9b32-f7e19083a355.png align="center")

**Step 6: Tagging for Order**

Next was Tags. I used a dropdown to pick tags I’d used before. Even though this is a test, I’m trying to stay in the habit of tagging. In a real job, tags may be the only thing keeping thousands of resources from becoming a mess!

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/12e4fbae-37b8-46d0-bf2a-73a77a9839e9.png align="center")

**Step 7: Review + Create**

Finally, the Review + Create page. Azure ran a quick validation to make sure my logic was sound. Everything looked good, so I hit Create. A status bar appeared, and a few seconds later: VNet successfully created!

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/501ac3e2-3401-44c4-8b4d-fb9c207e122e.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/cb72601e-0a68-44cb-812e-f22ff2a2013b.png align="center")

**Step 8: Splitting the Island (Creating the Subnet)**

Now that I had my island (VNet), I needed a specific town (Subnet).

1.  I went to the left pane under Settings and clicked Subnets.
    
2.  I noticed Azure had already created a "default" subnet for me, but I wanted to make my own.
    
3.  I clicked + Subnet at the top.
    
4.  I gave it a name and told Azure how many IP addresses this specific neighborhood should have.
    

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/cd3422a6-d97f-447c-9722-0b4d9666577a.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e0019e3d-20f9-4ecb-bb9b-3923fb58a4a7.png align="center")

**Mission Accomplished!** I can now see both my new subnet and the default one living side-by-side.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/590eb3cc-109f-4305-8d7b-4bb49df7145e.png align="center")

**What I Learned:** Building a VNet isn't about clicking buttons; it's about deciding how much space your business needs and how you want to arrange your resources for maximum security. I'm starting to see the "big picture" of how cloud infrastructure is mapped out!

**Skills Trained and Developed:** Virtual Network (VNet) Provisioning, Network Segmentation & Subnetting, IP Address Management (IPAM), CIDR Notation & Subnet Masking, Azure Resource Hierarchy (Subscriptions & Resource Groups), Cloud Security Service Evaluation (Firewall, DDoS, Encryption), Metadata Tagging, Geographic Region Selection & Data Residency, Azure Portal Navigation & Deployment Monitoring.