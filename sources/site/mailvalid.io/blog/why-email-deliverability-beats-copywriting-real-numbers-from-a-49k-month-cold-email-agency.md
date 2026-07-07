# Source: https://mailvalid.io/blog/why-email-deliverability-beats-copywriting-real-numbers-from-a-49k-month-cold-email-agency

# Why Deliverability Beats Copywriting: Real Numbers from a $49K/Month Cold Email Agency

![](https://cdn.sanity.io/images/lv62vofw/production/0774681c4639720d7fd71feec2119a6cffdce36c-609x343.jpg) There is a widespread assumption in cold email that the copy is everything. Get the subject line right, nail the first sentence, personalise the opener - and the meetings will follow.

A cold email agency that grew from zero to nearly $50K/month with no website, no SEO, and no paid ads spent years learning that this assumption is wrong. Not partially wrong. Fundamentally wrong.

The copy matters. But a copy that lands in spam folders generates zero replies no matter how good it is. The unsexy, underappreciated part of cold email - list quality, email verification, domain infrastructure, and inbox placement - is what separates agencies billing $5K/month from agencies billing $49K/month.

This post breaks down what that infrastructure evolution actually looks like in numbers, what the agency learned at each stage, and what it means for anyone running outbound today.

## TL;DR

- The agency grew from $0 to $49K/month in roughly 7 years with no website - 90%+ of revenue came from outbound and referrals
- Early campaigns ran at 11% open rates from a burned domain sending via a newsletter tool; switching to a dedicated cold email platform pushed open rates to 38% almost overnight
- Bad enrichment sources were returning 30% invalid email rates - money spent on data that couldn't be delivered
- Switching to a higher-accuracy enrichment source brought pre-verification accuracy to 82–85%, cutting waste on the verification step
- Current benchmarks across 23 active campaigns: 54% average open rate, 3.8% average reply rate, 340–380 meetings/month
- The agency now caps sends at 25–30 emails per inbox per day and rotates domains every 3–4 months proactively
- Infrastructure cost has grown from $0 in 2018 to ~$2,800/month - accepted as the standard cost of doing business
- The founder's verdict: "The technical infrastructure matters way more than the copy."

Ready to verify your email list?

100 free credits. $0.001/email after. No credit card required.

[Start Free](https://mailvalid.io/register)

## What "11% Open Rate" Actually Means

In the early days, this agency was sending cold outreach from a Gmail account through a mass email tool - the kind built for newsletters, not outbound prospecting. Open rates sat at 11%. Replies were near zero.

An 11% open rate in cold email almost always means one thing: the emails aren't reaching the inbox. Not that the subject line needs work. Not that the audience is wrong. The domain was already flagged, the sending tool had a poor reputation with ISPs, and the emails were being silently sorted into spam or promotions tabs before a single recipient ever had a chance to read them.

Switching to a dedicated cold outreach platform - one with proper sending infrastructure and reputation management - pushed open rates from 11% to 38% in a single month. The copy didn't change. The audience didn't change. The infrastructure changed.

This is the lesson most early-stage outbound practitioners learn too late: deliverability is upstream of everything else. Your reply rate is a function of your open rate, which is a function of your inbox placement rate, which is a function of your domain health, which is a function of your bounce rate, which is a direct function of list quality.

Fix list quality first. Everything else cascades from there.

## The 30% Invalid Email Problem

Here's a number that should shock anyone running outbound at scale: at one point, this agency was pulling contact lists from enrichment tools that returned 30% invalid email addresses.

Three in ten emails. Bad. Not soft-bounce bad. Hard-bounce bad - addresses that don't exist, domains that are gone, mailboxes that were deleted months ago.

What does 30% invalidity do to a campaign?

- **Bounce rate goes to 10–30%** - ISPs (Gmail, Outlook, Yahoo) flag senders at 2%. Above that threshold, you're in active filtering or deferral territory.
- **Domain reputation drops** - hard bounces are the clearest possible signal to a mail server that you're sending to stale, unverified lists. Repeated bouncing accelerates domain blacklisting.
- **The campaign is wasted** - every email you pay to send, every hour of copy you write, every contact you research - 30% of it goes nowhere.

The agency eventually moved to a higher-quality enrichment source that delivered 82–85% accuracy before any verification step. The verification layer still ran - but the reject rate dropped significantly because the underlying data was cleaner. Less waste. Better deliverability. Same budget.

## The Infrastructure That Runs 23 Campaigns Simultaneously

This is what the agency's outbound stack looks like today, after years of iteration:

| Layer | What It Does | Why It Matters |
| --- | --- | --- |
| Enrichment | Finds and formats contact emails | Data quality sets the ceiling on everything downstream |
| Verification | Confirms email validity before sending | Keeps bounce rate under 2%; protects domain reputation |
| Inbox infrastructure | Manages sending domains, DNS, warmup | Prevents blacklisting; maintains inbox placement |
| Sending platform | Sequences, personalisation, tracking | Delivery of the actual campaign |
| Monitoring | Tracks inbox placement, domain health | Catches deliverability degradation before it kills a campaign |

The agency currently runs 180 inboxes across all client campaigns. Managing that at any meaningful quality requires a dedicated infrastructure person - they have one whose entire role is domains, DNS, warmup monitoring, and inbox health.

Sending volume is capped at 25–30 emails per inbox per day. In 2018, 200 emails per day per inbox was normal and worked fine. That ceiling has compressed dramatically as ISPs have become more sophisticated about detecting cold email patterns. The economics of cold email at scale now require more inboxes sending less volume each - which means domain acquisition, warmup, and rotation are ongoing operational costs, not one-time setup tasks.

Every new inbox goes through a minimum 21-day warmup before cold sends begin. Domains are rotated proactively every 3–4 months even if they're still healthy - because by the time a domain shows signs of reputation damage, it's already affecting deliverability.

## What a 9% Difference in Inbox Placement Actually Costs You

The agency mentioned something in passing that deserves more attention: for roughly six months, a significant portion of their emails were landing in spam - and they didn't know, because they weren't monitoring inbox placement.

Consider what that means numerically. If you're sending 10,000 emails per month and your inbox placement drops from 95% to 85%:

| Inbox Placement | Emails Seen | Reply Rate | Replies |
| --- | --- | --- | --- |
| 95% inbox rate | 9,500 emails seen | 3.8% reply rate | 361 replies |
| 85% inbox rate | 8,500 emails seen | 3.8% reply rate | 323 replies |

Difference: 38 fewer replies per month - from a 10% shift in placement that you might not even notice without monitoring.

At an average meeting conversion of 25% from reply, that's roughly 9–10 missed meetings per month. For an agency charging $3K–5K/month per client, inbox placement degradation is a direct revenue leak - and it's invisible without measurement.

The fix isn't copywriting. It's monitoring, and the root cause is almost always list quality degrading domain reputation over time.

## The Verification Step That Changed Their Numbers

The agency's enrichment-to-send workflow that runs on 15 out of 23 active campaigns:

### Step 1: Enrich contacts from a high-accuracy source

Not all enrichment tools are equal. The agency's move from multiple lower-quality sources to a single higher-accuracy tool dropped their pre-verification invalid rate dramatically. Starting with 82–85% accuracy means verification has less work to do - and means you're paying for verification on mostly-valid addresses, not burning credits on obviously bad data.

### Step 2: Verify every email before it enters the sequence

Even at 82–85% pre-verification accuracy, 15–18% of addresses need to be filtered out before sending. At scale (the agency runs thousands of contacts per campaign), that's a meaningful volume of bounces prevented.

What verification checks:

- Syntax and format (RFC 5322 compliance)
- Domain existence and MX record validity
- SMTP-level mailbox confirmation (does this specific inbox accept mail?)
- Disposable email detection
- Catch-all domain flagging
- Role-based address identification (info@, noreply@, support@)

Only addresses passing all relevant checks go into the sending sequence. Catch-all addresses get a separate treatment - lower volume, monitored closely, promoted or suppressed based on engagement.

### Step 3: Send only to verified addresses

This is non-negotiable at the agency's scale. The math is simple: if a campaign has 1,000 contacts and 15% are invalid, sending unverified means 150 hard bounces - pushing the domain's bounce rate well above the 2% ISP threshold in a single campaign. One unverified send can damage a domain that took three weeks to warm up.

## Current Benchmarks: What "Good" Looks Like

For context on what a well-run cold email operation produces when infrastructure is prioritised:

| Metric | Agency Current Average |
| --- | --- |
| Open rate (across 23 campaigns) | 54% |
| Reply rate (average) | 3.8% |
| Meetings booked / month | 340–380 |
| Emails per inbox / day (cap) | 25–30 |
| New inbox warmup period | 21 days minimum |
| Domain rotation frequency | Every 3–4 months |
| Active inboxes | 180 |
| Monthly infrastructure cost | ~$2,800 |

These numbers reflect years of iteration on infrastructure, not years of iteration on subject lines. The 54% open rate isn't a copywriting achievement - it's a deliverability achievement. Emails that land in the inbox, from warmed domains with clean sending histories, from lists where invalid and risky addresses have been filtered out before sending.

## What This Means If You're Running Outbound Today

The agency's trajectory maps cleanly onto a set of principles any outbound practitioner can apply regardless of scale:

### 1\. Your open rate is a deliverability diagnostic, not a copy diagnostic.

If your open rates are under 30%, the copy is not the problem. The domain, the sending platform, or the list is the problem. Fix those before you rewrite a single subject line.

### 2\. Enrichment quality sets the ceiling on your verification efficiency.

Cheap or low-quality enrichment that returns 30% invalid data wastes your verification budget and degrades your domains. Invest in higher-accuracy enrichment first; verification is the safety net, not the primary filter.

### 3\. Verify every list before every send.

No exceptions. This isn't optional at any scale. A single high-bounce send can damage a domain that took three weeks to warm. The cost of verification - fractions of a cent per email - is trivially small compared to the cost of domain rehabilitation or replacement.

### 4\. Monitor inbox placement, not just open rates.

Open rates are measured among emails that were opened. They tell you nothing about the emails that went to spam and were never seen. Inbox placement monitoring tells you what percentage of your sends are actually reaching the inbox - the only number that matters.

### 5\. Infrastructure cost is a fixed cost of doing cold email properly.

The agency spends ~$2,800/month on infrastructure for $49K/month in revenue - roughly 5.7% of revenue. That's not overhead waste. That's the minimum investment to do outbound without burning domains and clients.

## How MailValid Fits Into This Stack

The verification step in this agency's workflow - and in any serious outbound operation - requires a tool that checks at the SMTP level, not just syntax. Most list invalidity isn't format errors. It's mailboxes that no longer exist on valid domains: the kind only an SMTP handshake can detect.

MailValid runs all four verification layers in a single API call:

- Syntax validation (RFC 5322)
- Domain and MX record check
- SMTP mailbox verification (without sending an email)
- Enrichment flags: disposable, catch-all, role-based, free provider

At $0.001 per email - verified results only, credits never expire - verifying a 5,000-contact campaign list costs $5. At the agency's scale of 23 active campaigns running thousands of contacts each, verification is a line item, not a budget conversation.

```python
import requests

API_KEY = "mv_live_your_key"
emails = [...]  # your enriched list

resp = requests.post(
    "https://mailvalid.io/api/v1/verify/bulk",
    headers={"X-API-Key": API_KEY, "Content-Type": "application/json"},
    json={"emails": emails}
)

results = resp.json()["results"]

# Only send to valid, non-risky addresses
send_list = [
    r["email"] for r in results
    if r["status"] == "valid"
    and not r["is_disposable"]
    and not r["is_role"]
]
```

What you get back per address: status (valid / invalid / catch-all), is\_disposable, is\_role, is\_catch\_all, is\_free\_provider. Enough to make clean segmentation decisions before a single send goes out.

## Frequently Asked Questions

### What is the acceptable bounce rate for cold email campaigns?

The acceptable hard bounce rate for cold email campaigns is under 2%. ISPs including Gmail and Outlook use bounce rate as a primary sender reputation signal. Senders consistently above 2% experience increased spam filtering, deferred delivery, and - if sustained - domain or IP blacklisting. Most professional cold email agencies target below 1% by verifying lists before every send.

### Why do open rates matter less than inbox placement rate?

Open rates only measure opens among emails that were delivered and seen. If 20% of your sends go to spam, those are invisible in your open rate calculation - they never had a chance to be opened. A 40% open rate on 80% inbox placement means only 32% of your total sends were actually seen and opened. Inbox placement rate is the more fundamental metric; open rate is a subset of it.

### How often should cold email domains be rotated?

Professional cold email agencies typically rotate sending domains every 3–4 months, proactively - not waiting for visible reputation damage. By the time domain health metrics degrade, the deliverability impact has already affected campaign performance for weeks. Proactive rotation maintains clean sending histories and avoids the warmup gap that occurs when a burned domain must be replaced mid-campaign.

### What is the difference between email enrichment and email verification?

Email enrichment is the process of finding email addresses for contacts - using tools that match names, company domains, and known patterns to generate or locate email addresses. Email verification is the process of confirming those addresses are valid and deliverable, using SMTP-level checks. Enrichment generates the address; verification confirms it works. Both are required: high-accuracy enrichment reduces verification costs; verification prevents bounces that enrichment alone cannot eliminate.

### How many cold emails should you send per inbox per day?

The current safe sending volume for cold email inboxes is 25–30 emails per inbox per day - down significantly from earlier periods when 100–200/day was common. ISPs have become more sophisticated at detecting high-volume cold sending patterns, and exceeding per-inbox limits accelerates domain reputation degradation. Running more inboxes at lower volume per inbox is the standard approach for scaling cold email while maintaining deliverability.

### What is email warmup and why is it required?

Email warmup is the process of gradually increasing sending volume from a new inbox over a period of weeks, establishing a positive sending history with ISPs before cold campaigns begin. A new inbox that immediately sends 30 cold emails per day raises immediate red flags for spam detection systems. Professional agencies use a minimum 21-day warmup period before any cold sends go out from a new inbox, typically using automated warmup tools that exchange emails between real inboxes to build engagement signals.

### Does email copy quality matter if deliverability is poor?

No. An email that lands in a spam folder generates zero replies regardless of copy quality. Deliverability - inbox placement rate, domain reputation, bounce rate management - is a prerequisite for copy quality to matter at all. The practical implication: if reply rates are low, diagnose deliverability first. If inbox placement is healthy and open rates are reasonable, then copy optimisation becomes the lever to pull.

## The Real Lesson from Seven Years of Cold Email

The agency that grew from $0 to $49K/month with no website spent years obsessing over copy while quietly building the infrastructure foundation that actually made the copy work.

When they finally started tracking inbox placement, they found months of sends going to spam - good copy, bad infrastructure, zero results. When they fixed the data quality and verification step, their invalid rate dropped from 30% to under 15%. When they systemised warmup and domain rotation, open rates stabilised at 54%.

The copy stayed roughly the same throughout. The infrastructure changed everything.

If you're running cold email at any scale - one campaign or twenty-three - the verification step is non-negotiable. It's the cheapest intervention in the stack and the one with the most direct impact on whether your emails reach a human being at all.

→ Start verifying with 100 free credits - no credit card required → View pricing for bulk list verification → Read the API docs

All performance data referenced in this article reflects publicly shared figures from a real cold email operation. Names and identifying details have been omitted. Last updated: June 2026.

M

MailValid Team

Email verification experts

Share:

Join developers who verify smarter

### Stop letting bad emails hurt your deliverability

100 free credits. $0.001/email after. Credits never expire. No credit card required.

[Start Free — 100 Credits](https://mailvalid.io/register) [Read the Docs](https://mailvalid.io/docs)

### More from MailValid

[The Real ROI of Email Verification for Cold Email Campaigns\\ \\ Cold Email](https://mailvalid.io/blog/email-verification-roi-cold-email-campaigns) [How a 2% Bounce Rate Silently Destroys 40% of Your Email Deliverability\\ \\ Email Deliverability](https://mailvalid.io/blog/how-bounce-rates-silently-destroy-email-deliverability)

Verify 100 emails free [Start Free](https://mailvalid.io/register)