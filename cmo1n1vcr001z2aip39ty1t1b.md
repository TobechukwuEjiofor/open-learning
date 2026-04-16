---
title: "My First Cloud VM ☁️"
datePublished: 2026-04-16T15:32:50.814Z
cuid: cmo1n1vcr001z2aip39ty1t1b
slug: my-first-cloud-vm
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/2d741a57-9155-4a3a-96be-329b3f976003.jpg

---

My cloud learning journey has been smooth sailing so far, but I knew the time was coming to get my hands dirty with the technical core of infrastructure. Today was that day.

I set out to create a Virtual Machine (VM) in Azure, host a web server, and access it via SSH. Here is the step-by-step breakdown of how I did it, and the "mistake" that almost made me give up.

**Navigating the Azure Portal**  
I started by searching for Virtual Machines on my Azure home page. Since this was my first time, the dashboard was empty—a blank canvas. I clicked the Create button to get started.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/791b9af2-6e25-4137-87e5-40520a2dfe9c.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/973518c2-9606-4ae9-843b-205f977969ba.png align="center")

**Configuring the Basics & Security**  
Azure requires a bit of "paperwork" before it gives you a server. I filled out the basics: Subscription, Resource Group, Region, and Image (Ubuntu).

During this stage, I had to make a key networking decision. I configured the Inbound Port Rules to allow two specific types of traffic:

**• Port 22 (SSH):** So I could remotely access the terminal from my laptop.  
**• Port 80 (HTTP):** So that once I installed a web server, the rest of the world could actually see the website.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/08e2e802-7ea8-4025-a977-2cc9c640b9e0.png align="center")

**Disks, Networking, and The "Family" of Resources**  
I moved through the Disks and Management tabs, sticking mostly to the defaults for this test. Under the Networking tab, I noted that Azure automatically created a Virtual Network (VNet) and a Subnet specifically for this VM.

Once I hit "Create," I checked All Resources and was surprised to see that "one VM" actually created a whole family of supporting resources: a Network Interface (NIC), a Public IP Address, a Network Security Group (NSG), and a Disk. It made me realize that a VM isn't just a lone computer; it's a whole ecosystem.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/8670fb09-de59-40d6-9f94-aee0132a5f68.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/dfc2c0af-aed4-46f9-8b17-c7b6d5d51693.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e8a089ce-f8ef-4562-8a21-757fb9235ff0.png align="center")

**Securing the Keys to the Kingdom**  
Before finalizing, I reached a critical step: Downloading the private key (.pem file). I made sure to save this securely. In the world of SSH, this key is your only way in. Without it, your VM is essentially a locked vault.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/6cb4099b-a4cc-4f99-9db2-5562f4ed11a2.png align="center")

**Accessing the VM and Launching a Web Server**  
To actually "enter" my new server, I opened Windows PowerShell on my laptop. I used the SSH command provided by Azure, replacing the placeholder with the actual file path to my .pem key.

I hit Enter, watched the authentication code flash by, and... I was in!

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/f36940fa-dbcf-4a85-858b-36c97979ee92.png align="center")

I started with the standard maintenance housekeeping:

`sudo apt update` (to check for updates)

`sudo apt upgrade` (to upgrade)

**Installing Apache2**  
With the system updated, I decided to turn this empty VM into a functional web server by installing Apache2:

`sudo apt install apache2`

After the installation finished, I took the VM's Public IP address and pasted it into my web browser. Because I had opened Port 80 earlier, the "Apache2 Ubuntu Default Page" loaded up instantly. 😊

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/7b02cdbd-378f-4657-8a77-348fb9b9ac02.png align="center")

**The "Expired" Mystery**  
I took a short break to celebrate, but when I returned, I couldn't get back in. Every SSH attempt failed. I tried for hours, feeling that familiar "tech frustration" creeping in.  
The next day, I found the culprit: My Azure subscription had expired. Because the subscription was inactive, the VM had been deallocated. It was a simple fix, but a huge lesson in monitoring account health. I’m glad I didn’t give up!

**Skills Learned & Takeaways**  
\- **Cloud Resource Provisioning:** Navigating the Azure Portal to deploy a Linux-based Virtual Machine.  
\- **Infrastructure Components:** Understanding the roles of VNets, Subnets, and NICs.  
\- **Network Security:** Configuring Inbound Port Rules (Port 22 & 80) to balance management access with public web traffic.  
\- **SSH Protocol:** Using private keys for secure remote authentication via PowerShell.  
\- **Web Server Administration:** Installing and verifying an Apache2 server on a remote Linux environment.  
\- **Troubleshooting:** Learning that infrastructure reliability starts with active subscription management.