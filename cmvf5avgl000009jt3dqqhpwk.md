---
title: "I rebuilt an insecure Azure setup with Terraform, then destroyed it and rebuilt it from a fresh clone"
datePublished: 2026-10-19T11:02:40.610Z
cuid: cmvf5avgl000009jt3dqqhpwk
slug: i-rebuilt-an-insecure-azure-setup-with-terraform-then-destroyed-it-and-rebuilt-it-from-a-fresh-clone
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/dfb827ea-cf5d-454c-bf00-d091ea49d115.jpg

---

I wanted a portfolio project that showed I could build something secure on Azure, not only something that runs. So I invented a client.

**Bridgeline Pay** (fictional) is a small fintech. A contractor built its Azure setup by clicking through the portal. The app server was public, SSH was open to the world, a forgotten home IP sat in the database firewall, and the database password lived in a team chat. A partner bank then sent a security questionnaire, and nobody could answer it.

My job was to rebuild the setup as code and answer five questions with evidence.

### **What I built**

Everything was written in Terraform. Nothing was created in the portal.

*   A virtual network with public, private and database subnets
    
*   A bastion VM, reachable by SSH from my IP only
    
*   An app VM with **no public IP**, reachable only through the bastion
    
*   A public load balancer, the only way in from the internet
    
*   A PostgreSQL database with **no public access**
    
*   A Key Vault holding the generated database password
    
*   Terraform state in an Azure storage account with locking, versioning and a firewall
    

That came to 34 resources.

### **The five questions, and the proof**

1.  **Is the database reachable from the internet?** No. Azure reports public access as disabled, and the server name does not resolve from outside. Port 5432 was open from the app VM and closed from the bastion.
    
2.  **Who can log in?** Only my IP, to the bastion. A direct SSH attempt to the app VM timed out.
    
3.  **Where are secrets?** In Key Vault. The app VM reads the password through a managed identity, with no stored credential. I searched every git commit for the password and found nothing.
    
4.  **Can you rebuild it?** I destroyed all 34 resources, cloned the repo fresh, and rebuilt. Then I destroyed it again to close out.
    
5.  **Where is the state?** Remote, locked and firewalled.
    

**Four decisions worth explaining**

**A private VM still needs to reach the internet.** It has to install nginx on first boot. I turned off Azure's default outbound access, then gave the load balancer an explicit outbound rule. Every outbound connection leaves from one known address, and the VM waits for that rule before it boots.

**No password on the VM.** The VM has a managed identity with the "Key Vault Secrets User" role, which can read secrets and nothing else.

**The password is also in the state file.** Terraform stores generated values in state. It is not in git, and the state storage is private, but it is there. I wrote this down as a limitation instead of hiding it.

**Making a database private takes three things.** I needed a delegated subnet, a private DNS zone linked to the network, and an NSG that allows port 5432 from the app subnet only. I ended the NSG with an explicit deny-all, because Azure's default rules let every subnet in a network talk to every other.

### **What broke**

*   **My Ed25519 SSH key was rejected.** Azure wanted RSA.
    
*   **Dropped connections looked like Terraform errors.** About a fifth of my calls to Azure's management endpoint failed on my home network. On my phone hotspot, none of 20 did. I spent a long time reading Terraform errors before I measured it.
    
*   **A stuck state lock.** A failed plan never released it. I checked that the lock was mine, that nothing else was running, and then used force-unlock.
    
*   **My plan predictions were wrong twice.** I expected an in-place change and got a forced replacement of a subnet. Azure also quietly added a service endpoint to the database subnet, and Terraform wanted to remove it.
    
*   **Order matters for identities.** Adding a managed identity to an existing VM and using it in a role assignment in the same plan failed. I applied the identity first with -target.
    
*   **My allow-lists broke whenever my IP changed.** The state storage, Key Vault and bastion rule all depended on it.
    

### **What I did not do**

No HTTPS or domain. One app VM and one database, so no redundancy. Key Vault still has a public endpoint behind a firewall. Shared-key access is still on for the state storage. The state storage account is created by a separate bootstrap configuration, because Terraform needs somewhere to keep state before it can create anything. I installed psql on the VM by hand for the login test.

### Skills I used

1.  **Infrastructure as Code (Terraform)**. 34 resources written as code, with variables, generated passwords, plan files, -target and force-unlock. I destroyed and rebuilt it from a fresh clone to prove it was repeatable.
    
2.  **Cloud networking (Azure)**. Public, private and database subnets, NSG rules with an explicit deny-all, and a public load balancer with an outbound rule.
    
3.  **Cloud security.** A bastion host, so the app VM needed no public IP. Secrets in Key Vault, read through a managed identity. A private database, and no secrets in git history.
    
4.  **Also practised:** private DNS and a managed PostgreSQL server, SSH jumps and cloud-init on Linux, remote state with locking, and diagnosing real network failures from measurements.
    

### **What I learned**

*   Measure a shaky network before you debug the code.
    
*   Read the plan before every apply. It caught my mistakes twice.
    
*   "Secure" means being able to prove it. Each claim above has a command and an output behind it.
    
*   Being able to destroy and rebuild from a fresh clone is the real test of infrastructure as code.
    

The code and README are on GitHub: [https://github.com/TobechukwuEjiofor/azure-secure-environment.git](https://github.com/TobechukwuEjiofor/azure-secure-environment.git)

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/4478955a-04f6-4136-9868-24cf2a0a3d2b.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/5cfbfa73-5c39-47aa-928b-76c93ca1cc4c.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/fa9d46bf-eca4-41b5-9f7e-43b01a44c0c5.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/674b2f09-4dcf-470f-89fe-854a301d8764.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/663903a4-fd9e-4c8d-b60b-a447d3d256a9.png align="center")