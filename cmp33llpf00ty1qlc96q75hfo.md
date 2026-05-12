---
title: "Further Into Networking: The "SIM Card" of Azure – NIC"
datePublished: 2026-05-12T20:43:33.798Z
cuid: cmp33llpf00ty1qlc96q75hfo
slug: further-into-networking-the-sim-card-of-azure-nic
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/90517031-cb5c-4e8e-98ef-a7ca871695c9.png

---

A wise man (my cloud trainer) once told me, *"If you can master networking in the cloud, you have the strongest foundation to understand everything else."*

I’ve spent quite some time ensuring I don’t just know the theory, but also how these concepts work in a live environment.

Today, I’m looking at two key components: **Network Interface Cards (NICs)** and **Route Tables**.

## What is a Network Interface Card (NIC)?

Think of a NIC as the **SIM Card** for your Virtual Machine (VM).

Just like a phone needs a SIM card to connect to a network and get a phone number, a VM needs a NIC to connect to a Virtual Network (V-Net) and get an IP address. Without a NIC, your VM is "offline" and cannot communicate with anything.

**Some Key Features:**

*   **Identity:** The NIC holds the IP configuration (Private and Public).
    
*   **Flexibility:** A VM can have two NICs, and it must have at least one.
    
*   **Persistence:** You can delete a VM but keep the NIC, allowing you to move that "identity" or IP address to a different VM later.
    

## What is a Route Table?

A Route Table is a set of rules (called **routes**) that act like a GPS for your network traffic. It determines where data goes when it leaves a resource.

By default, Azure provides built-in "System Routes" that handle traffic automatically. However, professionals create **Custom Route Tables** when they need to override the defaults. For example, forcing all internet traffic to go through a specific Firewall for security before it reaches the web.

**Pro-Tip:** Route Tables are actually attached to **Subnets**, not individual VMs. This ensures that every VM within that subnet follows the exact same traffic rules. 

## Practical / Demo

Today, I’ll be demonstrating how to replace a default NIC with a custom-built one. Because, as stated earlier, in production (live environments), you shouldn't stick with the default setup Azure gives you. You may also want to swap a NIC to move a specific IP address to a new VM during a migration, upgrade your network speed to handle more data, or isolate traffic for better security.

### Step 1: Resource Group and VM Setup

I started by creating a Resource Group, V-Net, Sub-net and a Virtual Machine – we have already gone through this in earlier posts, so I will not dwell on it. Azure assigned it a default Public IP: **52.188.12.64**. My goal is to delete this default identity and replace it with a custom one. 

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/140b2b26-538d-4fa5-92b2-37e33144c31d.png align="center")

### Step 2: Preparing the VM

I SSH’ed into the VM using GitBash and ran the standard maintenance commands:

`ssh -i tobechukwuvm_key.pem tobechukwu@52.188.12.64`

`sudo apt update`

`sudo apt upgrade` 

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1550ff8f-9ff4-41e9-8fae-4a26faab19be.png align="center")

I was in.

### Step 3: Creating and Attaching the New NIC

Back in the Azure Portal, I navigated to the VM's **Networking** settings.

**A Challenge:** I tried to attach a new NIC while the VM was running and received an error.

**The Solution:** I had to **Stop** the VM first. In the cloud, certain "hardware" changes require the machine to be powered off.

Once stopped, I created the new NIC, assigned it to my V-Net, and selected my Network Security Group (NSG) and required ports. Azure then attached it to the VM.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/2b43b2b8-84ee-496e-ab9a-657026ddb8cf.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/f8bb3a5c-2261-4528-a122-ee45b38ee3ec.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e9c84c69-fd66-4406-bdaf-f7c09e1323ff.png align="center")

### Step 4: Swapping the "SIM Cards"

I now had two NICs attached. I detached the default one created by Azure. Immediately, the VM Overview page showed **no Public IP address**—it had lost its old identity.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/eea0ae1a-ed9d-4c24-b899-f853a5ff3251.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a8a149f9-7840-4f00-8d10-58f2975b757d.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e59d5379-9484-4f28-8b04-1a1b4c3346f9.png align="center")

### Step 5: Configuring the New Public IP

To get the new NIC online, I navigated to **Network Settings > IP Configurations**. I clicked **Configure** under the Public IP section, followed the prompts to create a new Public IP, and saved the changes.

Returning to the VM Overview page, I saw my brand new IP address successfully displayed.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/b80006f3-f3be-455d-a277-66127c453798.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/ac331242-d662-4e53-afb7-f0cf7e1a7c79.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/53bd80a6-1695-4f98-a8ea-0a4914be408b.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a73d7148-5a23-4f4a-af22-cb5364b44c89.png align="center")

## Why does this matter?

The process wasn't just about changing a set of numbers. It was a practical exercise in **Infrastructure Management**. In real-world scenarios, you will need to swap NICs for several reasons: to move an IP to a new server, troubleshoot network connectivity, or upgrade security without losing your network configuration. 

## Skills Learned & Used

During this demo, I applied several core Cloud and DevOps competencies:

*   **Azure Portal Navigation:** Managing resources, stopping/starting VMs, and configuring networking properties.
    
*   **Virtual Networking:** Understanding the relationship between V-Nets, Subnets, and NICs.
    
*   **Infrastructure Troubleshooting:** Identifying why a NIC cannot be attached to a running VM (State Management).
    
*   **Linux Administration:** Using SSH and GitBash to manage a remote server and perform system updates via CLI.
    
*   **IP Configuration:** Manually assigning and configuring Public IP resources in a cloud environment.
    
*   **Routing Logic:** Understanding the difference between System Routes and Custom Route Tables for traffic control.