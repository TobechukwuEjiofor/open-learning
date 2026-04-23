---
title: "Diving Further into Azure Networking:  Peering 🍐"
datePublished: 2026-04-23T23:12:17.561Z
cuid: cmoc3jonq009l1qiab897d60k
slug: diving-further-into-azure-networking-peering
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/7dc341c8-038e-4add-aa0f-b5ba1070e9f0.png

---

Today, I'll be diving a little deeper into Azure Networking. My instructor asked if two V-Nets in a Resource Group could communicate with each other, because V-Nets are designed to be completely isolated from each other.

The question was, for example, "if a business has a V-Net for Marketing and Sales, and another for their App and Website, are resources in one network able to communicate with the other? Can there be a direct connection to link website visits to the sales v-net as leads?".

Turns out the answer is yes, through a... 'bridge', called Peering. Peering allows traffic to travel via Microsoft’s private backbone network—not the public internet—keeping the connection fast and secure. Here is how I set my first one up:

**Organizing the Lab**

First, I created a dedicated Resource Group for this demo.

Rookie-Tip: Always create separate resource groups for each test. It makes "tearing down" the infrastructure easy once you're done, ensuring you don't get billed with hefty, unexpected charges (in dollars 👀)

**Creating the V-Nets**

Next, I created two Virtual Networks: *V-Net A* and *V-Net B*. I used a /24 CIDR range for both, which provides 256 IP addresses each.

However, there is a "Golden Rule" in peering: CIDR ranges cannot overlap. If the IP addresses in V-Net A are the same with V-Net B, Azure won't know where to send the traffic—it would be like trying to peer a network with itself.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/e6cc62f7-934c-4345-8c46-b80a40fe5b32.png align="center")

To avoid this, I customized the octets:

• V-Net A: 10.0.0.0/24  
• V-Net B: 10.0.1.0/24

By changing that third octet from 0 to 1, I ensured both networks had unique address spaces.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/ee11480a-ffb8-4aa1-96e1-d418e4ca579f.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a7e68a45-8402-428a-83d2-2f29328cba71.png align="center")

**Deploying the Virtual Machines**

Next, I deployed one VM into each network. When creating these, I made sure to select the correct V-Net in the Networking Tab for each respective machine.

• VM-A sits in V-Net A  
• VM-B sits in V-Net B

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/9f50e40e-1673-4fa8-bc30-a2417f947658.png align="center")

**The "Before" Test**

I used GitBash to SSH into VM-A using my private key. Once inside, I attempted to communicate with VM-B using its Private IP address (10.0.1.4) via the ping command:

`ssh -i vm-a_key.pem vm-a@40.xx.xx.216   ping 10.0.1.4`

The result? **Nothing**. The cursor just kept blinking. Since the networks were still isolated, VM-A had no idea that VM-B existed.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/9faaea9e-dd53-4aaa-80a2-3667ae3abcbb.png align="center")

**Setting up the Peering**

Now for the fix. I navigated to the V-Net A overview page in the Azure Portal:

1.  Scrolled down to Settings > Peerings.
    
2.  Clicked Add.
    
3.  Gave the peering a name and selected the target V-Net (V-Net B).
    
4.  Azure automatically handled the "reciprocal" link (it peered A to B and B to A simultaneously).
    

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a3cd53d6-b4ec-4536-8d55-156a981e3fc1.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/b735b5d8-e02d-40f3-9229-ece2ead3c41a.png align="center")

**The "After" Test: Success!**

As soon as I got the "Deployment Successful" message, I checked my GitBash terminal. The cursor that was previously stuck started spitting out lines of data! The packets were flowing.

VM-A was now successfully talking to VM-B.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/c7ff469a-a0bb-4d7b-9c54-089fe946b747.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/85457218-9ddd-45f9-8669-93a357108097.png align="center")

**Final Thoughts**

This test took me about 1 hour and 30 minutes, but seeing the successful ping results was incredibly rewarding. It’s one thing to read about "isolated networks," and it’s another to actually build a bridge to connect them.

*Crucial last step:* Don't forget to delete that Resource Group! Clean up your environment so you can live to test another day.

**Skills Learned & Takeaways**

• Virtual Network Architecture: Designing and deploying isolated Virtual Networks (V-Nets) within the Azure ecosystem.

• IP Addressing & Subnetting: Understanding and implementing CIDR ranges (specifically /24) and the critical importance of avoiding overlapping IP address spaces in a peered environment.

• V-Net Peering: Configuring bi-directional peering to enable secure, low-latency communication between disparate networks via the Microsoft backbone.

• Network Troubleshooting: Using the ping utility to verify connectivity and diagnosing "silent" communication failures in isolated infrastructures.

• Remote Management: Utilizing GitBash and SSH for secure remote access to Linux Virtual Machines.

• Operational Efficiency: Implementing "Resource Group lifecycle management" to optimize cloud costs and ensure environment cleanliness.

• Strategic Thinking: Applying cloud networking concepts to real-world business scenarios, such as bridging Marketing and Sales data pipelines.