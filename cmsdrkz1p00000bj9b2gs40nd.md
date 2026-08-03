---
title: "Deploying a Linux VM on Azure with Terraform"
datePublished: 2026-08-03T21:51:44.003Z
cuid: cmsdrkz1p00000bj9b2gs40nd
slug: deploying-a-linux-vm-on-azure-with-terraform
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/04d2928a-c69c-4137-9d09-88dd2ca4c5f2.png

---

Honestly, this demo tested my patience. But I got through it; here's exactly what happened.

### The Goal

Deploy a Linux Virtual Machine on Azure using Terraform - infrastructure as code, no clicking around the portal. Just write the commands, run them, done.

So I thought.

### Setting Up

The `main.tf` started straightforward enough: provider block, resource group, VNet, subnet, NIC, NSG, and finally the VM resource. One thing I did early that saved me later was referencing the resource group location across all resources instead of hardcoding it everywhere:

`resource "azurerm_resource_group" "terraform-rg" {   name = "terraform-rg"   location = "eastus"   }`

Then everywhere else:

`location = azurerm_resource_group.terraform-rg.location`

This meant that whenever I needed to change regions (and I did, multiple times, lol) I only had to change it in one place. Everything else updated automatically. Small habit, big payoff.

### Error 1: The SSH Key Mistake

First `terraform plan` looked good and fine until I hit this:

`Error: "admin_ssh_key.0.public_key" is not a complete SSH2 Public Key`

I had written the SSH key block like this:

`admin_ssh_key {   username = "azureuser"   public_key = ("~/.ssh/id_rsa.pub")   }`

The problem? I was passing the file path as a plain string instead of telling Terraform to actually read the file. The fix was wrapping it in Terraform's `file()` function:

`admin_ssh_key {   username = "azureuser"   public_key = file("~/.ssh/id_rsa.pub")   }`

Simple fix, but an easy mistake to make when you're new to Terraform's syntax.

### Error 2: Quota Restrictions

With the SSH key sorted, I ran `terraform apply` and hit a wall:

`OperationNotAllowed: Operation could not be completed as it results in exceeding approved standardDv5Family Cores quota. Current Limit: 0, Additional Required: 4`

My subscription had zero quota for the Dv5 VM family. To find out what was actually available, I ran:

`az vm list-usage --location eastus --output table`

This returned a quota table showing which VM families had available cores. The key ones with headroom were **Standard BS Family** and **Standard D Family**, both with a limit of 10 cores. I updated my VM size to:

`size = "Standard_B2s"`

### Error 3: No Capacity in East US

New error:

`SkuNotAvailable: The requested VM size Standard_B2s is currently not available in location 'eastus'.`

This one isn't about quotas, Azure simply didn't have enough physical hardware available in East US for that size at that moment. The fix was changing the region to UK South:

location = "uksouth"

Because I had structured my config to reference the resource group location everywhere, that single change cascaded across all resources automatically.

### Error 4: No Capacity in UK South Either

At this point I was starting to feel personally targeted by Azure.

`SkuNotAvailable: The requested VM size Standard_B2s is currently not available in location 'uksouth'.`

B2s was exhausted in UK South too. So I switched VM families entirely, away from B series and into D series:

`size = "Standard_D2s_v3"`

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/7b4fd243-8020-403a-9979-0177cd387455.png align="center")

### It Finally Worked

`azurerm_linux_virtual_machine.terraform-vm: Creating... azurerm_linux_virtual_machine.terraform-vm: Still creating... [00m10s elapsed]`  
`azurerm_linux_virtual_machine.terraform-vm: Still creating... [00m20s elapsed]`  
`azurerm_linux_virtual_machine.terraform-vm: Creation complete after 22s`

`Apply complete! Resources: 1 added, 0 changed, 0 destroyed.`

`Outputs: vm_public_ip = "20.x0.xxx.xxx"`

22 seconds. After all that, the actual VM creation took 22 seconds. The public IP came through as an output, which I had configured in Terraform to surface automatically, and I was able to SSH straight in:

`ssh azureuser@20.x0.xxx.xxx`

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/566aebf1-fcc6-45f9-8faf-18304f0cb77c.png align="center")

### What I Learned

A few honest takeaways from this session:

**Always save before running any Terraform command.** I learned early that `terraform fmt`, `terraform plan`, and `terraform init` all read from disk — not from what's open in your editor.  
Unsaved changes are invisible to Terraform.

**Quota errors and capacity errors are different things.** Quota is a subscription-level limit set by Microsoft, you can request increases. Capacity means Azure has run out of physical hardware for that size in that region, you have to move region or change size.

**Structure your Terraform to reference, not repeat.** Using `azurerm_resource_group.terraform-rg.location` instead of hardcoding `"eastus"` everywhere made region changes a one-line fix instead of a find-and-replace nightmare.

**Errors are just information.** Every error in this session told me exactly what was wrong and pointed toward the fix. The key is reading them carefully rather than panicking. Even though a little panic is warranted lol.

The VM is running. Infrastructure as code works. We keep learning, and on to the next one.

Thank you for reading.