---
title: "The No-Code Portfolio: Hosting A Live Static Website via Microsoft Azure Storage 🚀"
datePublished: 2026-04-01T22:03:39.521Z
cuid: cmngleohs008w2eld2jjr0hup
slug: the-no-code-portfolio-hosting-a-live-static-website-via-microsoft-azure-storage
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/97949013-0978-4bcf-8067-b87f91667a59.png

---

I practiced this a few weeks ago, and it blew my mind.

Sometimes, you don’t need a complex, heavy website. If you just need a sleek portfolio, a resume, or a simple blog, you can get it live and fully functional in *less than 30 minutes.*

If you have an active Azure subscription, you are ready to go.

Here is how I did it:

**Step 1: The Foundation (Storage Account)**

Everything on the cloud needs a home. For a static website, that home is a Storage Account. I’ve already written a step-by-step guide on how to set one up right [here](https://tobetheson.hashnode.dev/setting-up-shop-mastering-the-azure-hierarchy), so check that out first to catch up!

**Step 2: Creating the "Container"**

Once your storage account is ready, you need a specific folder to hold your website files. In Azure, these are called Containers.

• Just give your container a name, hit create, and you’ve got your digital "folder" ready.

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/ab662b6c-5b96-4630-840e-0205481d7f28.png align="center")

**Step 3: Using AI as Your Web Designer**

You don't need to be a coder to make a beautiful site. I used ChatGPT to write my HTML for me.

Here is the exact prompt I used:

`"Create a clean, modern, single-page HTML portfolio. Include a Hero section with my name (Tobechukwu Ejiofor) and tagline (Design. Data. Cloud). Make it a dark theme with a 'glassy' feel and smooth animations. Ensure it is mobile-friendly and has placeholders for my project images."`

(Tip: If you have specific text or photos you want to use, include them in your prompt so the AI can build them right into the code!)

**Step 4: The Notepad Trick (Important!)**

When the AI gives you the code:

1.  Copy the code.
    
2.  Open Notepad on your computer and paste it.
    
3.  The Key Part: When you click "Save As," don't save it as a .txt file. Name it index.html. This tells the computer (and Azure) that this is a website file, not just a document.
    

**Step 5: Preparing Your Images**

Take your project photos and rename them exactly how they appear in your HTML code (e.g., project1.jpg). This ensures the website knows exactly which picture to show in which location.

**Step 6: Going Live**

Now, go back to your Azure Container and upload your index.html file and your images. To make sure the world can see it:

• Click the three dots ... on your container.

• Select Change Access Level.

• Pick Anonymous read access. This makes your site "Public."

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/a6538dbf-52fb-4a83-bd75-d312dc885877.png align="center")

**The Result**

I opened my file link, pasted it into my browser, and my portfolio was live!

![](https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/65065e56-6914-45b4-a26b-0d6caa0821f8.png align="center")

**What I learned:**

• How to create and manage Storage Accounts.

• How to use Containers to organize web data.

• How to turn a simple idea into a live cloud asset without writing a single line of code myself.