---
title: "Securing Private VMs: A Hands-On Guide to NSGs and Azure Bastion"
datePublished: 2026-06-04T10:57:46.049Z
cuid: cmpzdsuuz000e1sp9d2mi5bw6
slug: securing-private-vms-a-hands-on-guide-to-nsgs-and-azure-bastion
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/03e6b06d-eaf8-4820-94da-4c7677e62303.png

---

**Introduction**

As part of my recent cloud learning group assignment, my team was tasked with solving a real-world cloud security challenge. Instead of working in isolation, we brainstormed, collaborated, and tested individual approaches before aligning on a final architecture.

Our mission was successful, and we successfully deployed, secured, and documented the entire solution. Here is a breakdown of the challenge and how we built the architecture step-by-step.

**The Challenge: Secure VM Access with NSG Rules + Azure Bastion**

**Scenario:** A school chain requires private Virtual Machines (VMs) with absolutely no public IP addresses. Only authorized administrators should be able to connect via Azure Bastion, backed by Network Security Group (NSG) rules that explicitly block all other inbound traffic.

**Project Tasks:**

1.  Deploy a VM with no public IP address in a dedicated private subnet.
    
2.  Configure NSG rules to allow only SSH via Bastion, while explicitly denying all other inbound traffic.
    
3.  Connect to the VM successfully via Azure Bastion and document the full process.
    
4.  Analyze potential failure points by explaining what would break if the NSG configuration failed.
    

**Core Cloud Security Concepts Demonstrated:**

*   Private Workloads: Minimizing the attack surface by eliminating public IPs.
    
*   Network Segmentation: Using isolated subnets and tailored NSGs to control traffic flow.
    
*   Secure Administration: Utilizing Azure Bastion for seamless, secure browser-based management.
    

**Step-by-Step Implementation**

**Step 1: Create the Resource Group**

We started by setting up a dedicated Resource Group to keep all our project components neatly organized and managed under one lifecycle.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/c8966d95-4661-41f9-8cf9-9fe30b204b11.png align="center")

**Step 2: Provision the Virtual Network (VNet)**

Our architecture requires both the target VM and the Azure Bastion service to reside within the same Virtual Network (VNet) boundary to communicate internally.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/d86b8cac-1205-48f6-8d89-12777146d8d7.png align="center")

**Step 3: Configure the Required Subnets**

We segmented our VNet into two distinct subnets:

*   **VM Subnet (**/24**):** Sized to provide plenty of private address space for current and future virtual machines.
    
*   **AzureBastionSubnet (**/26**):** Azure strictly requires this dedicated subnet name and a minimum size of /26 to support Bastion's operational overhead and automatic scaling.
    

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1fa49968-894f-42a0-b413-719d34759e83.png align="center")

**Step 4: Create the Network Security Group (NSG)**

To act as our first line of defense, we provisioned a dedicated NSG to govern inbound and outbound traffic rules for our workloads.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/fd80fc18-8ddf-4d2f-96bf-02d74e58c660.png align="center")

**Step 5: Configure "Allow Bastion" and "Deny Inbound" Rules**

We created precise security rules to control ingress:

1.  Allow Bastion: An inbound rule allowing traffic on Port 22 (SSH) sourced specifically from the AzureBastionSubnet.
    
2.  Deny Inbound: A fallback rule to explicitly drop traffic from any other source.
    

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/959b2afb-56b3-48a0-bb17-1a2f4533b188.png align="center")

**Step 6: Associate the NSG with the Private Subnet**

An NSG does nothing until it's attached. We linked our freshly configured NSG directly to our VM's private subnet to enforce our traffic rules.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/832b6f1a-3db9-4ee3-ad07-14c2ad810b00.png align="center")

**Step 7: Deploy the VM (No Public IP)**

We provisioned our Linux VM, ensuring that the public IP setting was explicitly set to **None**. The VM relies entirely on its internal private IP address.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/b09193a0-a4ac-4003-81da-54c5d31b9a41.png align="center")

**Step 8: Deploy Azure Bastion**

We enabled Azure Bastion on our VNet, linking it directly to the predefined AzureBastionSubnet.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/6ba70fe1-832c-4d52-89eb-ee446aec6d42.png align="center")

**Step 9: Connect Safely Through Bastion**

With everything in place, we launched a secure SSH session directly from our web browser via the Azure Portal.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/884783b1-8f91-406e-bbd3-19438b6bf3a1.png align="center")

**Real-World Troubleshooting: What We Learned**

Theory doesn't always match practice! During our initial implementation, an overly broad custom *DenyAllInbound* rule accidentally interfered with Azure Bastion’s background management traffic, resulting in unstable, dropping SSH sessions.

Once we fine-tuned our rules and properly structured our standard Azure default rules, full connectivity was restored. This taught us a valuable lesson: **always understand the exact traffic requirements of cloud-managed services before applying heavy-handed deny rules.**

By the end of the project, our team achieved:

*   A completely private VM footprint.
    
*   Tight, NSG-based boundary control.
    
*   Fully functioning, secure Azure Bastion management.
    
*   Practical cloud troubleshooting experience.
    

**Architectural Deep Dive: What Breaks if the NSG is Misconfigured?**

Understanding how to fix a broken network is just as important as building a working one. Here are four common failure modes we analyzed:

**🚨 Scenario 1: Bastion Traffic Blocked**

*   **The Error:** If the NSG blocks Port 22 (SSH) traffic originating from the AzureBastion Service Tag or its subnet.
    
*   **The Impact:** Azure Bastion will completely fail to establish a handshake with the VM. Administrators will lose all remote management capabilities, leaving the VM isolated.
    

**🚨 Scenario 2: Overly Permissive Rules**

*   **The Error:** Allowing inbound access from Any source to Port 22.
    
*   **The Impact:** Threat actors on the internet could discover an open pathway to probe your VM. This entirely defeats the purpose of isolating your workload inside a private subnet and drastically increases your attack surface.
    

**🚨 Scenario 3: Incorrect Rule Priority**

*   **The Error:** Placing a catch-all "Deny" rule at a higher evaluation priority (e.g., Priority 100) than the "Allow Bastion" rule (e.g., Priority 200).
    
*   **The Impact:** Azure processes security rules sequentially from the lowest number to the highest. The firewall will hit the "Deny" rule first and immediately drop the traffic, ignoring the valid Bastion rule entirely.
    

**🚨 Scenario 4: Missing NSG Association**

*   **The Error:** Forgetting to link the NSG to either the Subnet or the Network Interface Card (NIC) of the VM.
    
*   **The Impact:** None of your custom security boundaries will be enforced. The network will fall back to default, unprotected behavior, leaving your environment improperly exposed.
    

**Wrapping Up**

This assignment was an amazing hands-on exercise in zero-trust architecture. Building it together forced us to slow down, look at the underlying networking logic, and handle actual cloud debugging.