---
title: "Building a Cloud-Native App from Scratch 💻"
datePublished: 2026-09-20T16:33:21.686Z
cuid: cmua1cfla000009gmawbn082q
slug: building-a-cloud-native-app-from-scratch
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/caa5e434-b6b6-4c2f-8ff0-2142fbdd90b8.jpg

---

There's a specific feeling you get when you paste a public IP address into your browser and see something *you built* load on the screen. Not on localhost. Not in a Docker container on your laptop. On the actual internet, running on a Kubernetes cluster in Azure, deployed automatically by a pipeline you wrote.

Well, that happened for me this week, for the first time ever - end to end, and of course I am going to record/document that here.

### The Project

I built and deployed **ClearLoop**, a customer feedback web application, using a full cloud-native stack. The application itself is simple: customers submit feedback with their name, email, category, rating, and comments. An admin dashboard displays all submitted responses with summary statistics.

But the application itself wasn't really the point.

The real project was answering one question: **how do you take code from your laptop to a live, production-grade deployment on Azure, fully automated?**

### The Toolbox

<table style="min-width: 50px;"><colgroup><col style="min-width: 25px;"><col style="min-width: 25px;"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>Layer</strong></p></td><td colspan="1" rowspan="1"><p><strong>Technology</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>Application</p></td><td colspan="1" rowspan="1"><p>Node.js + Express</p></td></tr><tr><td colspan="1" rowspan="1"><p>Database</p></td><td colspan="1" rowspan="1"><p>PostgreSQL</p></td></tr><tr><td colspan="1" rowspan="1"><p>Containerisation</p></td><td colspan="1" rowspan="1"><p>Docker + Docker Compose</p></td></tr><tr><td colspan="1" rowspan="1"><p>Infrastructure as Code</p></td><td colspan="1" rowspan="1"><p>Terraform</p></td></tr><tr><td colspan="1" rowspan="1"><p>Container Registry</p></td><td colspan="1" rowspan="1"><p>Azure Container Registry</p></td></tr><tr><td colspan="1" rowspan="1"><p>Orchestration</p></td><td colspan="1" rowspan="1"><p>Azure Kubernetes Service</p></td></tr><tr><td colspan="1" rowspan="1"><p>CI/CD</p></td><td colspan="1" rowspan="1"><p>GitHub Actions</p></td></tr><tr><td colspan="1" rowspan="1"><p>Source Control</p></td><td colspan="1" rowspan="1"><p>GitHub</p></td></tr></tbody></table>

## The Process: Phase by Phase

### Phase 1: Building the Application

I started by writing the Node.js application from scratch (with a lot of help from my tutor). The server handles three API endpoints: submitting feedback, retrieving all feedback, and returning a summary. The frontend is plain HTML with no frameworks, a clean feedback form and an admin dashboard that pulls live data from the API.

One thing I re-learned: **save before you run anything.** I lost count of how many times a command returned a confusing error that turned out to be because I hadn't saved the file I just edited. It is trivial, and I have been a victim too many times, but once again it became one of the most important habits I continued to build during this project.

I tested the app locally using Docker Compose, spinning up both the Node.js app and a PostgreSQL database as containers with a single command:

```plaintext
docker compose up --build
```

Seeing the feedback form load at `localhost:3000` and watching a submission appear live on the admin dashboard was the first of many small wins that kept me going.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/36963aaf-451c-481a-91ae-0ea6ac113cce.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/2d8785a3-a56e-4d4c-a2b4-6a22b8a2cf00.png align="center")

### Phase 2: Pushing to GitHub

This phase was straightforward but important. Everything lives in one repository: application code, infrastructure definitions, pipeline configuration. The structure looks like this:

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/26c167da-7b23-4f31-95df-793db6e201cd.png align="center")

One repository. The entire project. Such that anyone can clone it and understand the full system.

### Phase 3: Infrastructure as Code with Terraform

This was where things.. got real.

Instead of clicking through the Azure Portal, I wrote code that described the infrastructure I wanted, and Terraform built it. Three files. One command. Seven Azure resources created automatically:

`terraform apply`

What came out the other side:

*   A Resource Group to contain everything
    
*   An Azure Container Registry to store Docker images
    
*   An Azure Kubernetes Service cluster to run the application
    
*   A managed PostgreSQL database
    
*   Networking, identity, and firewall rules wired together automatically
    

The outputs after apply gave me everything I needed for the next phase:

*   `acr_login_server = "clearloopprodacr.azurecr.io"`
    
*   `aks_cluster_name = "clearloop-prod-aks"`
    
*   `db_host = "clearloop-prod-db.postgres.database.azure.com"`
    

**One (comforting) thing I learned about Terraform:** professionals don't necessarily memorise the syntax. They understand the concepts, what resources exist, how they relate to each other, what needs to be created before what. The documentation fills in the exact syntax. Understanding the why behind each resource is what actually matters.

### Phase 4: CI/CD Pipeline with GitHub Actions

This was the phase I was most looking forward to, and the one that tested my patience the most. The pipeline does five things automatically on every push to main:

1.  Checks out the code
    
2.  Logs into Azure using a Service Principal
    
3.  Builds the Docker image and pushes it to ACR
    
4.  Connects to the Kubernetes cluster
    
5.  Deploys the new image to Kubernetes
    

**The first roadblock** was the Azure authentication. The CLI command to create a Service Principal kept returning a `MissingSubscription` error, even though I was logged in and the subscription was visible. Apparently, this is a known limitation with personal Microsoft accounts. The fix was creating the Service Principal manually through the Azure Portal instead, which worked immediately.

**The second roadblock** was the Kubernetes deployment timing out. The pods were stuck in `CreateContainerConfigError`. Running `kubectl describe pod` revealed the issue: the Kubernetes Secret containing the database credentials hadn't been applied to the cluster yet. The `secret.yml` file is intentionally excluded from Git (it contains passwords), so the pipeline couldn't deploy it automatically.

The fix was applying it manually once:

`kubectl apply -f k8s/secret.yml`

`kubectl rollout restart deployment/clearloop-app`

After that, watching `kubectl get pods -w` and seeing the pods transition from `Pending` to `Running` was one of the best moments of the project.

### The Moment It All Came Together

When GitHub Actions showed every step green for the first time: checkout, login, build, push, deploy, and I opened a browser tab and pasted in the external IP address from:

```plaintext
kubectl get service clearloop-service
```

And ClearLoop loaded. On the public internet. Deployed automatically from a Git push.

That feeling is hard to describe. Two days of work, across four phases, suddenly visible as a single working system. Every piece connected exactly as designed.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/34ef3cbc-e109-4075-bca9-5f50081d9bbe.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a8aecc24-7f64-40ad-9d85-a99c613de6ff.png align="center")

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/1f600923-f4a1-4bcf-9284-4e8127afe0fc.png align="center")

### What I Learned

**The tools were not so difficult to learn. The mindset is the hard part.**

There were multiple moments in this project where the right move was to stop, read the error carefully, reason about what it was actually saying, and try something different. Not to give up and not to blindly retry the same thing.

The Azure credentials issue could have stopped the project entirely. The Kubernetes secret error was confusing because everything *looked* right. But each error was just information pointing toward the fix.

A few specific technical lessons:

*   **Always run** `terraform plan` **before** `terraform apply` — it shows you exactly what will be created before touching anything
    
*   `kubectl describe pod` is your best friend when pods won't start — it tells you exactly why
    
*   **Kubernetes Secrets must be applied to the cluster separately** from the rest of the manifests if they're excluded from Git — this is by design, not a bug
    
*   **The** `depends_on` **with health checks in Docker Compose** solves the database race condition that causes apps to crash on startup
    

**Exit code 0** means a process finished cleanly — if your container keeps restarting with exit code 0, the code ran but the server isn't staying alive

### The Repository

Everything is open source and available at:  
**github.com/TobechukwuEjiofor/clearloop-feedback**

The README covers local setup, the infrastructure code is fully commented, and the pipeline file documents every step. Feel free to fork it, break it, and learn from it.

*This is part of my ongoing cloud and DevOps learning journey. Six months of classes, real projects, real errors, documented openly. If you're on a similar path, keep going. The moment it clicks is worth every frustrating error message along the way :)*