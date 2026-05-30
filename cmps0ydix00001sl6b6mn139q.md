---
title: "Scaling Under Pressure👟☁️"
datePublished: 2026-05-30T07:23:45.298Z
cuid: cmps0ydix00001sl6b6mn139q
slug: scaling-under-pressure
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1c705d2d-c59a-4a3b-a495-5146307c4f8c.png

---

Today my learning journey brought me face-to-face with two absolute heavyweights in cloud infrastructure: **Azure Virtual Machine Scale Sets (VMSS)** and **Azure Load Balancers**.

To understand why these two services are inseparable, let's look at a scenario that hits very close to home.

**The Celebrity Shoutout Dilemma 📈**

Imagine I run an e-commerce website where I sell premium sneakers. Because it’s a new brand, it’s quiet with maybe 20 to 30 visitors a day. One virtual machine (VM) handles that easily.

But then, I hang out with my friend **Fusi**—who just happens to be a massive celebrity in Nigeria. He loves the kicks, records a quick video hyping up my brand, and tells his millions of followers to hit my website right now.

Suddenly, a massive wave of hundreds of shoppers hits the site simultaneously. The load is too much for my single VM, the server chokes, and *boom*—the website crashes.

Traffic spikes are *beautifully* unpredictable. When a site crashes like this, it’s usually because the infrastructure wasn't provisioned to scale. That is exactly where a **Load Balancer** and a **Virtual Machine Scale Set (VMSS)** save the day.

*   **Azure VMSS** lets you deploy and manage a group of identical VMs as a single unit. You create a template, give it scaling rules, and Azure automatically creates new VMs when traffic spikes and destroys them when things quiet down. It works 24/7.
    

*   **Azure Load Balancer** acts like a traffic warden, sitting in front of your VMs to distribute incoming clicks evenly so no single server gets overwhelmed.
    

Here is my step-by-step documentation of how I configured this from scratch.

**Phase 1: Setting up the Groundwork**

First things first, I set up the mandatory baseline infrastructure. I created a dedicated **Resource Group**, a **Virtual Network (VNet)**, and a **Subnet**. Since I’ve deeply covered these core network concepts in my previous lab posts, I jumped straight into the new stuff today!

**Phase 2: Deploying the Traffic Warden (Azure Load Balancer)**

I decided to spin up the Load Balancer first so it would be ready to receive our VMSS backend later.

1.  **Basics:** I searched for Load Balancers in the portal, clicked Create, and selected a Standard Load Balancer. I assigned it to my resource group, picked my region, and left the tier as regional.
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/de7aca05-78f8-4ffb-b442-90640944cd65.png align="center")
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/f52733c2-39ac-4adc-a175-305971a2eff7.png align="center")
    
2.  **Frontend IP Configuration:** This is the public-facing entry point for our users. I added a new frontend IP, named it, selected IPv4, and generated a public IP address.
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1196a043-49f5-4eb2-938e-8ea8aa13c06f.png align="center")
    
3.  **Backend Pools:** This is where the web servers sit. I added a backend pool, gave it a clear name, and mapped it directly to the VNet I created earlier.
    
4.  **Inbound Rules & Health Probes:** I created a load balancing rule to route traffic on Port 80 (HTTP) from the frontend IP straight to our backend pool. To make sure traffic only goes to healthy servers, I attached a Health Probe to constantly check if the instances are alive.
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/42304f7b-5fd7-45bb-a533-f874e82dc745.png align="center")
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/b0451581-d287-49bc-a468-1adf6c62e94c.png align="center")
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/017ec745-ae79-4a9b-b7e4-e142c89b4ef9.png align="center")
    

With that done, I reviewed the setup and successfully deployed the Load Balancer.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e3e767c2-5b1f-4182-baf5-2e8bf186fe09.png align="center")

**Phase 3: Creating the Army (Virtual Machine Scale Set)**

With our traffic warden active, it was time to build the identical server fleet.

1.  **Basics & Orchestration:** I searched for Virtual Machine Scale Sets, hit Create, and filled out the basics. When it came to Orchestration Mode, I noted the options: Flexible (allows mixed VM sizes) versus Uniform (forces completely identical VMs). I went with Uniform to keep my instances perfectly synchronized.
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/cb47d647-d35c-4a69-9d28-3e289b6a37f6.png align="center")
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1a032e9c-112a-44ca-8e95-69b019773f62.png align="center")
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/3f124027-2dc9-444c-9889-448c8e946c62.png align="center")
    
2.  **Scale Settings:** For this demo, I set the scaling mode to manual with an initial instance count of 2, chose an Ubuntu image, and set up my authentication credentials.
    
3.  **Networking Integration:** Under the Networking tab, I selected my existing VNet. Crucially, I toggled the "Use an Azure Load Balancer" option to On and linked it directly to the frontend and backend pools I built in Phase 2.
    
4.  **Management & Automation (Custom Data):** To save costs on storage accounts for this lab, I quickly disabled boot diagnostics under Management. Then, I skipped over to the Advanced tab.
    
    ![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/01faadf3-f200-48cf-864c-e584c0b90552.png align="center")
    

To ensure every new VM automatically turned into a working sneaker website, I used the **Custom Data** section to inject a bash startup script. This installs Apache, launches it, and outputs a custom HTML page displaying the specific instance's hostname:

> Bash
> 
> `#!/bin/bash`
> 
> `sudo apt-get update -y`
> 
> `sudo apt-get install apache2 -y`
> 
> `sudo systemctl enable apache2`
> 
> `sudo systemctl start apache2`
> 
> `HOSTNAME=$(hostname)`
> 
> `cat <<EOF | sudo tee /var/www/html/index.html`
> 
> `<!DOCTYPE html>`
> 
> `<html>`
> 
> `<body style="font-family:Arial;text-align:center;padding-top:100px;background:#f4f4f4">`
> 
> `<h1 style="color:#0078D4">Hello from $HOSTNAME</h1>`
> 
> `<p>Azure VMSS Instance Tobechukwu</p>`
> 
> `</body>`
> 
> `</html>`
> 
> `EOF`
> 
> `sudo systemctl restart apache2`

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/65efa926-4d43-4b40-857d-0420c7841e9f.png align="center")

I clicked **Review + Create**, waited for deployment to finish, and checked the VMSS overview. Awesome—two live, running virtual instances were successfully initialized!

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a8aba43e-4c1e-4768-9890-85031d500771.png align="center")

**Phase 4: The Reality Check (Troubleshooting Time 🛠️)**

This is the part of learning where things get real.

I confidently copied the **Frontend Public IP** of my Load Balancer, pasted it into my browser, pressed Enter, and... *nothing*. The page just kept loading.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/86d1c849-3f13-4646-aca4-cb1ac24999d5.png align="center")

As a growing student of the cloud, I had to pause and think: *The servers are up, the Load Balancer is configured, but why isn't traffic getting in?* 💡 **The Network Security Group (NSG)!**

I searched for the NSG associated with our network subnet. By default, Azure blocks inbound internet access. I navigated to **Inbound Security Rules**, clicked **Add**, and opened up **HTTP (Port 80)** to allow public web traffic through.

I went back to my browser, hit refresh on the Load Balancer IP, and...

**Success!** 🚀

The screen loaded perfectly, proudly showing the custom homepage tied to one of my specific VM instances.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e9176723-216e-4447-9b37-cca122ff6873.png align="center")

**Lessons & Skills Unlocked 🧠**

This lab was highly technical and pushed me out of my comfort zone (as is the norm recently), but it was incredibly fulfilling to watch it come together. By getting my hands dirty today, I unlocked several critical Cloud DevOps skills:

*   **High Availability Architecture:** Understanding how to eliminate single points of failure by load balancing traffic across multiple instances.
    
*   **Infrastructure Auto-Scaling:** Conceptualizing how platforms handle massive, unpredictable traffic spikes seamlessly without manual intervention.
    
*   **Automated Provisioning (Cloud-Init):** Using bash script injection (Custom Data) to automatically configure software environments the second a server boots.
    
*   **Cloud Networking & Security triage:** Mapping backend infrastructure and troubleshooting real-world firewalls/NSGs to safely expose resources to the web.
    

The next time Fusi drops a viral video, my infrastructure will be ready. On to the next lab! ☁️

*If you enjoyed this breakdown, please drop a comment below if you are also learning Azure, or let me know how you handle auto-scaling rules in your own deployments!*