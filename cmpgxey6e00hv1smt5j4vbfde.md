---
title: "I Built an AI-Powered News Digest Tool to Make My Job Easier"
datePublished: 2026-05-22T12:59:12.137Z
cuid: cmpgxey6e00hv1smt5j4vbfde
slug: i-built-an-ai-powered-news-digest-tool-to-make-my-job-easier
cover: https://cdn.hashnode.com/uploads/covers/69ac7a1286766ac3a6e408e9/f707d596-31df-46e1-b580-b8c1d52809c8.png

---

**A Problem I Kept Running Into**

I work as a Customer Lifecycle Management Officer at a bank. A big part of my job is staying on top of what's happening in the financial world — not just globally, but specifically anything that could affect our customers here in Nigeria.

Every other morning, I'd find myself jumping between websites. Nairametrics. Businessday. Punch. Reuters. Checking for CBN announcements, interest rate movements, new regulations, fintech developments — anything that might affect the three key customer segments I work across: Corporate & Investment Banking, Commercial & Institutional Banking, and Consumer & Business Banking.

It was taking time I didn't always have. And sometimes, I'd miss things. So I thought — what if I could automate this? What if every morning, before I even opened my laptop properly, a summarised briefing was already sitting in my inbox? I had Python downloaded on my computer. I'd never really used it for something like this. But I figured this was a good enough reason to start.

My cloud and DevOps learning journey had already given me a rough sense of how APIs, scripts, and automation fit together — so I wasn't starting completely from zero. What I needed was someone to help me connect the dots and walk me through the actual build. That's where Claude came in. I described what I wanted, and Claude helped me design the approach, wrote the script, and guided me through every error along the way — step by step, in plain language.

**The Idea**

The plan was simple in my head:

1.  Pull financial news from Nigerian and international sources every day
    
2.  Filter it down to only what's relevant to banking and finance
    
3.  Use AI to summarise it into bullet points, organised by segment
    
4.  Email it to myself automatically every morning.
    

Free tools only. No subscriptions. No paying for APIs.

**Here's what I ended up using:**

*   NewsAPI (free tier) — for pulling news articles
    
*   RSS feeds — from Nairametrics, Businessday, Reuters, FT, Bloomberg, CBN, and others. Completely free, no account needed
    
*   Groq AI (free tier) — for generating the summaries. I originally tried Google Gemini but kept hitting quota limits; Groq worked perfectly
    
*   Gmail SMTP — to send the email using Python's built-in library
    
*   Windows Task Scheduler — to run the whole thing automatically every morning at 6:30 AM
    

**Step 1: Getting My Free API Keys**

Before touching any code, I needed three things:

NewsAPI key — Sign up at newsapi.org. The free tier gives you 100 requests per day, which is more than enough.

Groq API key — Sign up at console.groq.com. No credit card required. Generous free limits (14,400 requests/day). This is the AI that reads the articles and writes the summaries.

Gmail App Password — This is what lets Python send emails through your Gmail account without using your actual password. Here's how to get one:

1.  Go to your Google Account → Security
    
2.  Make sure 2-Step Verification is turned ON
    
3.  Search for "App passwords" in the search bar
    
4.  Create one, name it anything (I called mine "NewsDigest")
    
5.  Copy the 16-character password Google gives you — it only shows once
    

**Step 2: Installing the Required Libraries**

With Python already installed, I opened Command Prompt and ran:

`pip install requests feedparser groq`

That's it. Three libraries. requests fetches web content, feedparser reads RSS feeds, and groq connects to the AI.

**Step 3: The Script**

The script does four things in sequence — fetch, filter, summarise, send.

Here's a simplified look at how each part works.

**Fetching News**

The script pulls from two places:

First, RSS feeds — these are free news streams that most major publications provide: RSS\_FEEDS = \[ ("Nairametrics", "https://nairametrics.com/feed/"), ("Businessday NG", "https://businessday.ng/feed/"), ("The Punch Business", "https://punchng.com/topic/business/feed/"), ("CBN News", "https://www.cbn.gov.ng/rss/rss.asp?feed=pressrelease"), ("Reuters Finance", "https://feeds.reuters.com/reuters/businessNews"), ("FT Africa", "https://www.ft.com/rss/home/africa"), \]

Second, NewsAPI — which lets you search for articles by keyword: queries = \[ "Nigeria banking finance", "CBN monetary policy Nigeria", "Nigeria fintech economy", \]

**Filtering**

Not every article from a business feed is relevant. The script checks each article's title and summary against a list of finance and banking keywords: GENERAL\_KEYWORDS = \[ "bank", "banking", "CBN", "interest rate", "MPR", "naira", "forex", "loan", "NIBSS", "fintech", "payment", "regulation", "inflation", "capital market", "remittance", ... \] If none of those words appear in the article, it gets skipped.

**Classifying by Segment**

Articles that pass the filter are then sorted into one of three segments based on more specific keywords. For example, words like "IPO", "bond", "capital market", and "NGX" point to Corporate & Investment Banking. Words like "POS", "mobile banking", "USSD", and "financial inclusion" point to Consumer & Business Banking.

**Summarising with AI**

This is where it gets interesting.

Each segment's articles are passed to Groq's AI model with a clear instruction:

`prompt = f""" You are a briefing assistant for a senior Nigerian bank executive.`

`Segment: {segment}`

`Write a concise briefing with 4-6 bullet points summarising the most important developments and their potential impact on Nigerian bank customers in this segment. Be specific. Use plain English. Each bullet should be 1-2 sentences. Do not add a title or introduction - just the bullet points.`

`Articles: {article_list} """`

The AI reads all the articles for that segment and writes a clean, plain-English summary. I added a 5-second pause between each segment to stay within rate limits:

`time.sleep(5)`

**Sending the Email**

Python's built-in `smtplib` handles the email delivery. The digest is formatted as HTML so it looks clean in your inbox — with a header, one section per segment, and bullet points:

`with smtplib.SMTP_SSL("smtp.gmail.com", 465) as server: server.login(GMAIL_ADDRESS, GMAIL_APP_PASS.replace(" ", "")) server.sendmail(GMAIL_ADDRESS, RECIPIENTS, msg.as_string())`

**The Issues I Hit Along the Way**

I won't pretend it all worked first try. Here's what went wrong and how I fixed it:

**SyntaxError: invalid decimal literal** — I had pasted my API key without quotation marks. Python needs text values wrapped in " ". Simple fix, easy to miss.

**SMTPAuthenticationError —** Gmail rejected the login. Turned out I needed to properly set up the App Password rather than use my regular Gmail password. Once I regenerated it correctly, this went away.

**Gemini quota errors (limit: 0) —** I originally used Google's Gemini API for the AI summaries, but kept hitting limit: 0 errors even with a new key. Google's free tier quota was either exhausted or tied to a restricted project. I switched to Groq, which worked immediately and has much more generous free limits.

**IndentationError —** When I edited the script in Notepad to switch from Gemini to Groq, I accidentally misaligned the spacing. Python is very strict about indentation. The fix was to replace the entire affected function cleanly rather than editing line by line.

**Step 4: Automating It with Windows Task Scheduler**

Once the script was running correctly, I scheduled it to run every morning without me having to do anything:

1.  Open Task Scheduler (search for it in Windows)
    
2.  Click Create Basic Task
    
3.  Set trigger to Daily at 6:30 AM
    
4.  Set the action to run Python with digest.py as the argument
    
5.  Set the "Start in" path to your script's folder
    

One important note: the task only runs if your computer is on at 6:30 AM. If it's off, you can set it to "Run as soon as possible after a scheduled start is missed" in the task's Settings tab — so it runs the moment you turn your computer on.

**The Result**

Every morning now, both my personal and work inboxes receive a digest that looks something like this:

🏢 Corporate & Investment Banking — 22 articles reviewed  
• The CBN's latest MPC communiqué signals a hold on rates, reducing near-term pressure on corporate borrowing costs...  
• NGX All-Share Index closed 1.3% higher, driven by banking sector gains ahead of Q2 earnings season...

🏦 Commercial & Institutional Banking — 9 articles reviewed  
• New CBN guidelines on trade finance documentation are expected to streamline LC processing for SMEs...

👤 Consumer & Business Banking — 24 articles reviewed  
• NIBSS instant payment volumes crossed a new daily record, reflecting continued growth in digital transactions... Clean. Organised. In my inbox before I start work.

**What I Learned**

I came into this not knowing how to write Python. I left having built something I actually use at work.

A few honest takeaways:

• Start with a real problem. The fact that this tool solves something I face daily kept me motivated when errors showed up.

• Errors are normal. I hit four separate issues before it worked. Each one was fixable with a bit of patience.

• Free tools are genuinely powerful. Everything here — the news sources, the AI, the email delivery, the scheduler — cost nothing.

• You don't need to understand everything to build something useful. I didn't fully understand every line of code. But I understood what each section was supposed to do, and that was enough.

**What's Next**

This sits nicely alongside my cloud engineering work because it touches on APIs, automation, and working with external services — just in a more practical, everyday context.

Next, I would probably explore hosting this in the cloud so it runs independently of my laptop being on. But that's a future post.

If you're thinking about building something similar — whether for finance news or any other industry — I hope this walkthrough gives you a clear enough path to start. Feel free to reach out if you get stuck on any of the steps.

* * *

Built with Python, Groq AI, NewsAPI, and a genuine need to save time at work.