# Source: https://mailvalid.io/faq

# Frequently Asked Questions

Everything you need to know about email verification

## General

### How does email verification work?

Our verification process uses a multi-step approach: First, we validate the email syntax against RFC 5322 standards. Then, we check if the domain exists and has valid MX (mail exchange) records. Finally, we perform an SMTP handshake with the mail server to confirm the mailbox exists—all without sending an actual email.

### What is a disposable email?

Disposable emails (also called temporary or throwaway emails) are addresses from services like Mailinator, 10MinuteMail, or Guerrilla Mail that users create for one-time use. They're often used to bypass signup requirements and typically become invalid within hours. Our API detects these to help you maintain list quality.

### What is a catch-all domain?

A catch-all domain is configured to accept emails sent to any address @domain.com, even if that specific mailbox doesn't exist. This makes SMTP verification inconclusive. We flag these domains so you can decide whether to accept them based on your risk tolerance—they may be valid, but we can't confirm the specific mailbox.

### What is a role-based email?

Role-based emails are addresses like info@, support@, sales@, or admin@ that represent a function or department rather than an individual. They often have multiple recipients or are managed by different people over time. We flag these because they may have higher bounce rates or lower engagement for personal outreach.

## Pricing & Credits

### Do credits expire?

No, credits never expire. Once you purchase credits, they remain in your account until you use them. This makes Mailvalid perfect for projects with variable verification needs—buy credits when you need them, use them at your own pace.

### What counts as one verification credit?

A fresh completed result uses 1 credit. Invalid addresses are billable because they are completed checks that help you avoid bounces. Duplicate emails in the same bulk upload, recently verified repeats, and inconclusive checks are free.

### How many free credits do I get?

Every new account gets 100 free credits. These credits never expire and let you fully test the API before purchasing more. No credit card required to sign up.

## API & Technical

### Is there a rate limit?

Yes, the default rate limit is 60 requests per minute per API key for single verifications. For higher limits, contact us to discuss your needs. Bulk verification endpoints have separate limits designed for processing large lists efficiently.

### What's the average API response time?

Our average response time is under 500ms for single email verifications. Most verifications complete in 200-400ms. SMTP verification may take longer for some domains depending on their mail server response times.

### What accuracy can I expect?

We guarantee 95%+ accuracy for deliverable/undeliverable determinations. Some limitations exist: catch-all domains can't be definitively verified, and some corporate mail servers block SMTP verification. We clearly flag these cases in the API response.

Still have questions?

[Contact Support](mailto:sachin@getmailvalid.com)