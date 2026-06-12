---
title: "Good-bye to the Azure Portal! Hello to  Azure CLI 👋"
datePublished: 2026-06-12T21:06:48.166Z
cuid: cmqbf2w8q00000ci987wwbxc1
slug: good-bye-to-the-azure-portal-hello-to-azure-cli
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/416fa01e-f24e-413a-ab73-031ea6a26725.png

---

In this cloud learning process, things are getting real.

I’ll be honest—I heard it before and refused to believe it initially, but I’ve finally had to admit that the majority of Cloud Infrastructure work isn't done via the Azure Portal. It’s done via the Command Line Interface (CLI).

**What exactly is Azure CLI?**

Azure CLI is a cross-platform command-line tool for creating and managing Azure resources. It gives you the full power of the Azure platform through text commands that you can type into a terminal, a script, or a DevOps pipeline. No browser required.

It’s open-source, cross-platform, and because Microsoft prioritizes it, it is always up to date with the latest features.

Here is how I set it up and the "magic" I performed with it.

**Phase 1: Installation & Setup**

I started by installing the CLI directly from my terminal using the Windows Package Manager:

`winget install Microsoft.AzureCLI`

Once installed, I opened Visual Studio Code and performed the following:

1.  Navigate to Root: Used "`cd`" to get to my base directory.
    
2.  Verify Installation: I checked my version to ensure everything was set up correctly, using this: `az --version`
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/6accacd3-7146-4771-bcb5-5f7925ad9f8b.png align="center")
    

3.  Login: I ran the login command, which triggered a browser pop-up to select my Azure profile using this: `az login`
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/09e8d2b2-27dd-487f-8ec2-cd6909a70123.png align="center")
    

4.  Select Subscription: I was prompted to pick my subscription by typing 1 and hitting enter.
    

**Phase 2: Building the Infrastructure**

Before building, I used `az --help` to explore the command list. Then, I started building.

1.  **Creating a Resource Group**  
    Every Azure resource needs a home. I created one called TobechukwuVM in the East US region:  
    `az group create --name TobechukwuVM --location eastus`
    

2.  **Networking (VNet & Subnets)**  
    Next, I needed a network for my VM to live in. I created a VNet with a 10.0.0.0/16 range.  
    `az network vnet create --resource-group TobechukwuVM --name TobechukwuVNET --address-prefix 10.0.0.0/16`
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/75425005-c077-497d-b947-621d694f6a3e.png align="center")
    

Next, I needed a subnet. I used the arrow-up key on my keyboard (a lifesaver!) to bring back the previous command and added a subnet:

`az network vnet create \`  
`--resource-group TobechukwuVM \`  
`--name TobechukwuVNET \`  
`--address-prefix 10.0.0.0/16 \`  
`--subnet-name TobechukwuSubNet \`  
`--subnet-prefix 10.0.0.0/24`

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/ac1abefe-db6b-419e-a4aa-403fe97621cb.png align="center")

To add a second subnet, I ran this specific command:

`az network vnet subnet create \`  
`--resource-group TobechukwuVM \`  
`--vnet-name TobechukwuVNET \`  
`--name TobechukwuSubnet2 \`  
`--address-prefix 10.0.1.0/24`

*Learning Moment:* I actually got an error on the second subnet because I forgot to include the "`--vnet-name`". The terminal was kind enough to show me examples of what the command should look like, which helped me fix it instantly.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/94f92297-8a42-4469-9f3f-49716eb251bb.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/eccbaa78-9063-4fa2-a564-4030a56176c8.png align="center")

**Phase 3: The Heavy Lifting (VM Creation)**

This was the toughest part of the day. I faced several errors here, but troubleshooting is where the real learning happens.

`az vm create \   --resource-group TobechukwuVM \`  
`--name ToobechukwuVirtualMachine \`  
`--image Ubuntu2204 \`  
`--size Standard_D2s_v3 \`  
`--admin-username tobechukwuuser \`  
`--generate-ssh-keys \`  
`--vnet-name TobechukwuVNET \`  
`--subnet TobechukwuSubNet`

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/106d5722-8407-4c7b-a67c-02704905d321.png align="center")

Some things that went wrong initially:

1.  Formatting: I used a hyphen instead of an underscore in the VM size.
    
2.  Region Availability: The specific size I wanted wasn't available in East US. I had to investigate and adjust until I found a configuration that worked.
    

**Phase 4: Installing NGINX & Going Public**

Once the VM was live, I used SSH to "enter" the machine and install a web server.

1.  SSH into the VM: `ssh tobechukwuuser@20.115.116.200`
    

2.  Update VM and Install NGINX: `sudo apt update && sudo apt install nginx -y`
    

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/8d7eafb3-2548-4d9f-90c6-d8ab50030f71.png align="center")

I tried pasting the Public IP into my browser, but it wouldn't load. Why? Port 80 (HTTP) was closed. I headed back to the CLI to open the gate:  
`az vm open-port --resource-group TobechukwuVM --name ToobechukwuVirtualMachine --port 80`

After the CLI gave me the success notice, I refreshed the page and voila! The NGINX welcome page appeared. It was fascinating to see resources I typed into a terminal actually appear in the browser.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/32553ef2-3905-4c66-8fa3-b61d55d49e49.png align="center")

**Clean Up**

As usual, to avoid unnecessary costs, I deleted the entire Resource Group. One command wiped everything out:  
`az group delete --name TobechukwuVM`

**Final Thought:** It was an intense session, but seeing how much faster the CLI is compared to clicking through the portal has completely changed my perspective.

**Key Skills Developed with this Task**

1.  **Command Line Proficiency:** Bypassed the GUI (portal) to interact with cloud infrastructure entirely via plain text commands and maximized terminal efficiency using command history shortcuts.
    
2.  **Infrastructure Provisioning:** Gained hands-on experience setting up core cloud building blocks from scratch, including Resource Groups, Virtual Networks (VNets), and Subnets.
    
3.  **Live Troubleshooting:** Developed engineering resilience by interpreting terminal error logs to fix syntax mistakes and resolve regional resource availability constraints.
    
4.  **Linux Server Administration:** Used Secure Shell (SSH) to log into a remote Ubuntu VM, updating the system and installing software via the command line.
    
5.  **Cloud Networking & Security:** Applied basic security rules by diagnosing a connection failure, identifying a blocked port, and opening Port 80 (HTTP) for public web traffic.
    

Thank you for reading, and learning.

Till next time, we move!