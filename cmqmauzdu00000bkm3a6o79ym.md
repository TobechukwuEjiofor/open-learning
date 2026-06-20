---
title: "Sample Portfolio Submission: Auto-Scaling Infrastructure on Azure "
datePublished: 2026-06-20T11:54:08.454Z
cuid: cmqmauzdu00000bkm3a6o79ym
slug: sample-portfolio-submission-auto-scaling-infrastructure-on-azure
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/be68c289-4f30-4e51-ac3e-b37f9a9166ae.png

---

This is an example of what can be included in your portfolio on Auto-scaling on Azure.

## Project Overview

In modern cloud architecture, one of the most critical capabilities is the ability to respond to changing demand automatically—scaling infrastructure up during traffic surges and back down when load subsides.

This project demonstrates the design and implementation of an auto-scaling compute layer on Microsoft Azure using **VM Scale Sets (VMSS)** and an **Azure Load Balancer**. To ground this in a real-world scenario, I simulated how a high-traffic fintech platform like PalmPay or OPay would handle festive-season payment surges without over-provisioning expensive resources year-round.

This implementation covers infrastructure provisioning, intelligent auto-scaling rule configuration, and load balancing. To include real-world production practices, I have also included a failure analysis at the bottom that details exactly what goes wrong when these systems are misconfigured.

## Problem Statement

A fast-growing fintech company processes millions of payment transactions daily. During festive periods (Christmas, New Year, Easter), transaction volumes spike by 300–500% above baseline.

*   **The Challenge:** Running enough VMs to handle peak load year-round is cost-prohibitive. Conversely, running only baseline capacity risks service degradation or outright outages at exactly the moments customer trust is most critical.
    
*   **The Solution:** Deploy a VM Scale Set that automatically provisions additional compute capacity when CPU load is sustained above a defined threshold, and gracefully decommissions excess instances when demand normalizes. This keeps performance high and costs strictly proportional to actual usage. 
    

## Architecture Overview

Before diving into the Azure Portal, it is important to understand what we are building and why each component exists:

*   **VM Scale Set (VMSS):** A group of identical VMs managed as a single resource. Azure adds or removes instances automatically based on defined rules. All instances run the same OS, configuration, and application.
    
*   **Azure Load Balancer:** Sits in front of the VMSS and distributes incoming traffic across all healthy instances. Customers always connect to one stable endpoint (the Load Balancer's public IP), regardless of how many VMs are running behind it.
    
*   **Autoscale Rules:** Metric-driven policies that tell Azure when to add instances (scale out) and when to remove them (scale in). The metric used in this project is CPU utilization—the most direct indicator of compute pressure.
    
*   **Availability Zones:** Physically separate data centers within the same Azure region. Distributing instances across zones ensures that a datacenter-level failure doesn't take down the entire service. 
    

## Task 1: Deploying the VM Scale Set with a Load Balancer

The first step is provisioning the core infrastructure—the VMSS itself, with defined capacity boundaries and a Load Balancer to handle traffic distribution. Getting this foundation right is critical because every autoscale decision Azure makes later will operate within the constraints defined here. 

### Step 1 — Create a Resource Group

As with every new project, I started by creating a dedicated Resource Group. This ensures that all resources for this project are organized in one logical boundary, making lifecycle management and cleanup easy. 

### Step 2 — Configure VMSS Settings

I opened the VM Scale Set creation wizard in the Azure Portal and configured the baseline settings across the tabs:

*   **Basics Tab:** Named the resource, selected the region, and defined the orchestration mode.
    
*   **Networking Tab:** Integrated the VMSS with an Azure Load Balancer, establishing the public IP endpoint for incoming traffic.
    

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/bca3b644-a84a-4074-b2d0-261ee70fcddc.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/454343b2-638e-453e-9550-7411abedf6ed.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/c576db81-c436-4fe2-a3b0-8264aa2d5339.png align="center")

## Task 2: Configuring Autoscale Rules

With the infrastructure deployed, the next step is programming the intelligence that makes it dynamic. Autoscale rules are metric-driven policies that Azure continuously evaluates. When a metric crosses a defined threshold for a specified duration, Azure executes a scaling action. Without these rules, a VMSS is completely static—it will sit at its default baseline instance count forever, regardless of how much load hits it.

### Step-by-Step Configuration:

1.  Navigated to the Scaling blade under the Availability + Scale section on the left panel of the VMSS.
    
2.  Switched the scaling option from Manual to Custom Autoscale.
    
3.  Scale-Out Rule (Adding Capacity): Configured a rule to increase the instance count when average CPU utilization exceeded 70% for a sustained period.
    
4.  Scale-In Rule (Removing Capacity): Configured a complementary rule to decrease the instance count when CPU utilization dropped below 30%, ensuring the environment shrinks back to baseline when traffic normalizes.
    

## Verification

After saving the rules, I navigated to the Azure Load Balancer backend pools to confirm that the initial VMSS instances were healthy, registered, and actively being monitored by the load balancer

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/9cb89998-0594-45f0-8d43-edabfea73570.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1eb7b131-7c0f-451a-9a01-590318457585.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/97d352c1-895b-453f-a4d7-e51f0c8407a7.png align="center")

## Task 3: Critical Analysis — Consequences of Autoscale Misconfiguration

Engineering isn't just about making things work; it's about understanding how they fail. Here is a breakdown of three common real-world misconfiguration scenarios and their operational impacts.

### Scenario 1 — Scale-Out Threshold Set Too Low

*   **Configuration:** CPU scale-out threshold set to 20% instead of 70%.
    
*   **What Happens:** Under normal operating conditions, a healthy server running typical web workloads will naturally sustain 20–40% CPU usage. Setting the scale-out threshold at 20% means Azure treats everyday, routine operations as a major surge event.
    
*   **The Impact:** The VMSS immediately triggers maximum scaling during normal business hours and stays there permanently. The platform's cloud costs multiply by 2–3x with zero improvement in performance or resilience. An auto-scaling feature designed to save money ends up actively wasting it.
    

### Scenario 2 — No Scale-In Rule Configured

*   **Configuration:** Scale-out rule exists, but no scale-in rule is created.
    
*   **What Happens:** During a massive festive season traffic surge, Azure correctly triggers the scale-out rule and provisions maximum instances to handle the load. However, when the festive season ends and traffic returns to normal baseline levels, the VMSS remains maxed out indefinitely because it has never been given an instruction to scale back down.
    
*   **The Impact:** The entire business case for utilizing a VMSS—cost efficiency—is completely defeated. This is one of the most common real-world cloud mistakes, resulting in massive, unexpected cloud bills long after a traffic surge is over.
    

### Scenario 3 — Cooldown Period Set Too Short

*   **Configuration:** Scale-out cooldown period set to 30 seconds instead of the standard 5–10 minutes.
    
*   **What Happens:** A sudden traffic spike triggers the first scale-out action. Azure initiates the creation of a new VM instance. However, because the cooldown period expires in just 30 seconds, Azure re-evaluates the system metrics before the new VM has even finished booting up. Since the new VM isn't ready to handle traffic yet, the CPU on the existing instances remains high. Azure sees this, assumes the current capacity is still failing, and triggers *another* VM deployment.
    
*   **The Impact:** This creates a dangerous loop known as **Scale Thrashing**. The system burns through its maximum infrastructure allowance within minutes. It drives up costs instantly without actually improving application performance in the short term, because the terminal is flooded with half-booted, unready virtual machines.
    

## Conclusion

Building this lab highlighted that cloud automation is a double-edged sword. While VM Scale Sets provide an incredible tool for matching infrastructure costs directly to consumer demand, they require precise metric calibration. A robust cloud infrastructure relies not just on setting up the automation, but on meticulously tuning the thresholds, rules, and cooldown guardrails to keep systems stable, performant, and cost-efficient. 

### Core Cloud & Technical Skills Summary

*   **Dynamic Infrastructure Automation:** Built automated, elastic compute systems (VM Scale Sets) that automatically expand during traffic surges and shrink during low-demand periods.
    
*   **Traffic Management:** Configured Azure Load Balancers to distribute traffic evenly across dynamic server pools using a single public IP.
    
*   **Terminal & Linux Fluency:** Bypassed the portal GUI entirely to deploy infrastructure via Azure CLI, and used SSH to manage remote Ubuntu Linux servers.
    
*   **Automation Tuning & Architecture:** Programmed metric-driven auto-scale rules and optimized regional high-availability by distributing instances across Availability Zones.
    
*   **Systems Troubleshooting:** Diagnosed deployment failure logs, fixed syntax/regional configuration errors, and analyzed deep architectural risks like *Scale Thrashing*.
    
*   **FinOps & Cost Hygiene:** Applied strict cloud cost optimization by scripting resource destruction to prevent cloud waste and accidental billing.