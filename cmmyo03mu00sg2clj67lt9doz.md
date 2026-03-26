---
title: "Setting Up Shop: Mastering the Azure Hierarchy"
datePublished: 2026-03-20T08:56:26.984Z
cuid: cmmyo03mu00sg2clj67lt9doz
slug: setting-up-shop-mastering-the-azure-hierarchy
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/f2002087-d694-418c-b6db-6efef5bc7461.png

---

**Setting Up Shop: Mastering the Azure Hierarchy**

In a previous post, we talked about Resource Groups—the "folders" of the cloud. But as I dive deeper in my journey into the Cloud, I understand that even folders need a filing cabinet, and that cabinet needs a room.

Azure organizes everything through a specific hierarchy:

**Management Groups > Subscriptions > Resource Groups > Resources.**

Today, I’m "setting up shop" by arranging my space according to these principles. Here’s how I did this from start to finish:

**Phase 1: Building the Filing Cabinet - Management Groups**

I started at the very top. From the Azure homepage, I searched for **Management Groups**.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/40f70ff6-80f7-458d-b767-1dd70f50947e.png align="center")

By default, everything sits under a "Tenant Root Group," but I wanted something of my own. I clicked **Create** and was prompted for a Management Group ID and Name.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/4782b053-8736-4747-bbcb-fc4ec4d35f84.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a330d411-36ba-48e7-b088-3ef407d73a4b.png align="center")

**Note:** Azure warned me that the ID can’t be changed after creation, so I made sure it was clean and consistent.

A quick status bar informed me the group was "under development," and after a few seconds and a refresh, my new management group was live.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/20c025af-1eff-4fac-a86e-f2cffbfe8d52.png align="center")

**Phase 2: Moving the Furniture - Subscriptions**

Now that I had my group, I needed to put something in it. I decided to move my existing subscription into this new hierarchy.

Inside my new Management Group, I selected **Add Subscription**. Since I only have one subscription right now, it was a straightforward choice.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/b9eff705-ee3c-486c-9275-0399ffbd73a9.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/6ec04b18-bf40-4702-a6b7-d1283bdd7fbc.png align="center")

Azure gave me an important heads-up: *moving a subscription can change access and policies.* This makes sense—when you move a box to a new room, the "house rules" for that room now apply. I hit save, and after a brief wait, my subscription was officially nested under its new parent.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/83237884-8ffb-460c-842a-c8f4abe34416.png align="center")

**Phase 3: Stocking the Shelves - Resources**

With the hierarchy set (Management Group -> Subscription -> Resource Group), it was finally time to create an actual "thing."

I stepped into the Resource Group I created in my previous post. It was empty—a blank canvas.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/50dec9f2-213d-44a6-be35-adaa615adfd6.png align="center")

I clicked **Create**, which opened the vast **Azure Marketplace**.

To keep things simple but functional, I searched for a **Storage Account**. This felt like the perfect "first resource."

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/5066450e-b617-474c-9ba2-b7d44074964f.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/3c587d5c-66e4-4b2e-98b8-1fd339f9b0a6.png align="center")

*   **Pre-population Power:** Because I started *inside* my resource group, Azure automatically filled in my subscription and group details.
    
*   **The Basics:** I filled out the essentials—picking the region, the workload type, and the redundancy level.
    
*   **The Launch:** I clicked through the tabs until I reached the final **Review + Create**.
    

**Mission Accomplished**

A few moments later, I got the "Deployment Complete" notification. My Storage Account was officially born into a perfectly organized family tree.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/ff8e8bb7-f6b7-4eca-9fcd-59df2b60dae8.png align="center")

**Takeaway:**

This process felt like a long journey, but it was incredibly rewarding. Understanding these organizational foundations isn't just about completing an assignment; it’s about building essential muscle memory I’ll need throughout my career in Cloud and DevOps.