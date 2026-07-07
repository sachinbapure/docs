# Source: https://mailvalid.io/

99.9% Uptime

# Email Verification API 
at $0.001/email

6x cheaper than ZeroBounce. 8x cheaper than NeverBounce. Same accuracy. Credits that never expire. Built for developers who ship fast.

@ MX ✓ SMTP ✓

Try it free right now

 Verify

No signup required to try. 100 free credits on signup.

[Get 100 Free Credits](https://mailvalid.io/register) [View API Docs](https://mailvalid.io/docs) Watch Demo

No credit card required  ·  Credits never expire

MailValid — Product Demo

 Your browser does not support the video tag.

At 100K emails: MailValid saves you $290 vs ZeroBounce ($100 vs $390). Same 95%+ accuracy. Credits never expire.

> "MailValid cut our email bounce rate by 90%. We switched from ZeroBounce and saved over $300 a month. The API is dead simple to integrate and the accuracy is spot on."

N

Nikhil

10ex.ai

## How it works

Three-step verification process ensures maximum accuracy

1

### Syntax Validation

RFC 5322 compliant email format validation. Catches typos and formatting errors instantly.

2

### Domain & MX Check

Verifies domain exists and has valid mail exchange records configured.

3

### Mailbox Verification

SMTP-level verification confirms the mailbox exists without sending an email.

## Simple API integration

One API endpoint. JSON in, JSON out. Integrate in minutes with any language or framework.

- Single & bulk verification endpoints
- Detailed response with 10+ data points
- Average response time under 2 seconds

cURL

```
# Verify a single email
curl -X POST https://mailvalid.io/api/v1/verify/single \
  -H "X-API-Key: mv_live_your_key" \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com"}'

# Response
{
  "success": true,
  "result": {
    "email": "test@example.com",
    "status": "valid",
    "is_valid": true,
    "is_disposable": false
  }
}
```

## Comprehensive detection

Every verification includes multiple data points

### Disposable Emails

Detect temporary email services

### Role-Based

Identify info@, support@, etc.

### Free Providers

Gmail, Yahoo, Outlook detection

### Catch-All

Domains that accept all emails

## Built for every use case

From signup forms to bulk list cleaning, verify emails wherever you need them

### SaaS Signup Verification

Verify emails at registration to prevent fake accounts, reduce spam signups, and improve user quality from day one.

### Email List Cleaning

Clean your marketing lists before campaigns. Remove invalid, disposable, and risky emails to protect sender reputation.

### E-commerce Checkout

Validate customer emails during checkout to ensure order confirmations and shipping updates reach them.

### CRM Data Hygiene

Keep your CRM clean with regular verification. Identify stale contacts and maintain high-quality lead data.

### Lead Generation

Verify leads in real-time as they come in. Filter out fake submissions and focus on real prospects.

## Why developers choose Mailvalid

Built by developers, for developers who need reliable email verification

### Sub-second response times

Average API response under 500ms. Perfect for real-time form validation without slowing down your UX.

### Credits never expire

Any credits you buy never expire — use them whenever you want. Choose a monthly plan for steady volume, or top up pay-as-you-go anytime.

### Developer-first API

Clean JSON responses, comprehensive docs, and code examples in every major language. Integrate in minutes.

### 95%+ accuracy rate

Multi-layer verification including syntax, DNS, MX records, and SMTP mailbox checks for maximum accuracy.

### Bulk verification support

Verify thousands of emails at once with our bulk API endpoint. Perfect for list cleaning and migrations.

### 99.9% uptime SLA

Enterprise-grade infrastructure with redundancy across multiple regions. Your verification never stops.

95%+

Accuracy Rate

<500ms

Avg Response Time

99.9%

Uptime SLA

10M+

Emails Verified

## Frequently asked questions

Everything you need to know about email verification

### How does email verification work?

Our verification process uses a multi-step approach: First, we validate the email syntax against RFC 5322 standards. Then, we check if the domain exists and has valid MX (mail exchange) records. Finally, we perform an SMTP handshake with the mail server to confirm the mailbox exists—all without sending an actual email.

### What is a disposable email?

Disposable emails (also called temporary or throwaway emails) are addresses from services like Mailinator, 10MinuteMail, or Guerrilla Mail that users create for one-time use. They're often used to bypass signup requirements and typically become invalid within hours. Our API detects these to help you maintain list quality.

### What is a catch-all domain?

A catch-all domain is configured to accept emails sent to any address @domain.com, even if that specific mailbox doesn't exist. This makes SMTP verification inconclusive. We flag these domains so you can decide whether to accept them based on your risk tolerance—they may be valid, but we can't confirm the specific mailbox.

### Do credits expire?

No, credits never expire. Once you purchase credits, they remain in your account until you use them. This makes Mailvalid perfect for projects with variable verification needs—buy credits when you need them, use them at your own pace.

### What counts as one verification credit?

One credit is used for each fresh completed verification: valid, invalid, catch-all, or do-not-mail. Duplicate emails in the same bulk upload, recently verified repeats, and inconclusive checks are free.

### Is there a rate limit?

Yes, the default rate limit is 60 requests per minute per API key for single verifications. For higher limits, contact us to discuss your needs. Bulk verification endpoints have separate limits designed for processing large lists efficiently.

### What's the difference between Mailvalid and ZeroBounce/NeverBounce?

Mailvalid focuses on being developer-friendly with simple pricing and a clean API. Unlike ZeroBounce, we don't charge extra for data enrichment features you may not need. Unlike NeverBounce (which offers only 10 free credits), we give you 100 free credits to test properly. Our credits never expire, and we offer transparent, pay-as-you-go pricing without monthly minimums.

## Works with your stack

RESTful API with examples in every major language

Python

Node.js

Ruby

PHP

Go

Java

[View API Documentation](https://mailvalid.io/docs)

## Email verification tips, delivered to your inbox

Developer guides, deliverability insights, and API best practices. No spam, unsubscribe anytime.

 Subscribe

## Email verification 101

Plain-English answers to the questions developers ask before integrating

### What Is an Email Verification API?

An **email verification API** is a real-time service that checks whether an email address is valid, deliverable, and safe to send to — without sending an actual email. MailValid's API validates syntax, domain records, MX records, and mailbox existence via SMTP handshake. Developers integrate it into signup forms, checkout flows, CRM imports, and marketing automation to prevent fake accounts, reduce bounce rates, and protect sender reputation.

Unlike monthly-subscription tools like ZeroBounce or NeverBounce, MailValid offers transparent **email verification pricing** at $0.001 per completed verification with credits that never expire. You pay for useful results, not retries or recent repeats. Our **email validation API** handles single checks in under 200ms and bulk verification of up to 10,000 emails per batch. Every response includes detailed flags: valid/invalid, disposable, role-based, catch-all, free-provider, and more.

### Email Verification API vs Email Validation API — What's the Difference?

The terms are often used interchangeably, but technically **email validation** refers to syntax and format checks (RFC 5322 compliance), while **email verification** goes deeper — confirming the domain exists, MX records are valid, and the mailbox accepts mail via SMTP. MailValid does both: syntax validation, domain/MX lookup, SMTP mailbox verification, disposable detection, and catch-all flagging in a single API call.

### Who Uses MailValid's Email Verification API?

Our customers include SaaS startups verifying signups, e-commerce platforms validating checkout emails, outbound sales teams cleaning cold-email lists, marketing teams protecting newsletter deliverability, and fintech companies meeting KYC compliance. If you collect email addresses anywhere in your funnel, a **real-time email verification API** pays for itself by preventing bounces, fake accounts, and wasted spend.

Ready to integrate? [Read the email verification API documentation](https://mailvalid.io/docs) or [start free with 100 credits](https://mailvalid.io/register) — no credit card required.

## Latest from the MailValid blog

Pricing comparisons, deliverability deep-dives, and integration guides for developers

[Email Marketing\\ \\ **Why Your Ecommerce Emails Go to Spam (Even With Perfect SPF/DKIM)**\\ \\ A 47K-subscriber store had perfect SPF/DKIM/DMARC and still lost inbox placement. Here's the real cause and a metric most teams never track.](https://mailvalid.io/blog/why-your-ecommerce-emails-go-to-spam-even-with-perfect-spf-dkim) [Email Verification\\ \\ **Bulk Cleaning 1M+ Email Contacts Without Blowing Your Budget: A Developer's Playbook**\\ \\ Clean 1M+ contacts without blowing your budget. A developer's playbook for bulk email list cleaning at scale: architecture, cost math, and API patterns.](https://mailvalid.io/blog/bulk-cleaning-1m-email-contacts-without-blowing-your-budget-a-developer-s-playbook) [Email Marketing\\ \\ **The Warm Email Outreach System That Books B2B Calls Without Spraying Cold Strangers**\\ \\ A full warm outreach system: scrape buying signals, verify every email, personalize with AI, and book B2B calls - with deliverability that actually holds.](https://mailvalid.io/blog/the-warm-email-outreach-system-that-books-b2b-calls-without-spraying-cold-strangers) [Email Deliverability\\ \\ **Why Email Deliverability Beats Copywriting: Real Numbers from a $49K/Month Cold Email Agency**\\ \\ A cold email agency grew from $0 to $49K/month with no website. Here's what their infrastructure evolution reveals about email list verification, bounce rates, and why clean data matters more than clever subject lines.](https://mailvalid.io/blog/why-email-deliverability-beats-copywriting-real-numbers-from-a-49k-month-cold-email-agency) [Technical Guide\\ \\ **9 Ways to Turn 900K Public Case Studies Into High-Intent Prospect Lists**\\ \\ Learn how to mine casestudies.com for B2B buying signals, build lookalike lists from proven buyers, and use buyer language to write outbound emails that actually land - then verify every address before you send.](https://mailvalid.io/blog/9-ways-to-turn-900k-public-case-studies-into-high-intent-prospect-lists) [Technical Guide\\ \\ **How to Clean a 500K-Contact Email List Before Your Next Campaign**\\ \\ Step-by-step guide to bulk email list cleaning for large sends. Learn how to validate your email list, remove invalid addresses, and protect sender reputation.](https://mailvalid.io/blog/how-to-clean-500k-email-list-before-campaign)

[Browse all articles](https://mailvalid.io/blog)

## Ready to verify emails?

Start with 100 free credits. No credit card required. Set up in under 5 minutes.

[Get Started Free](https://mailvalid.io/register) [View Pricing](https://mailvalid.io/pricing)