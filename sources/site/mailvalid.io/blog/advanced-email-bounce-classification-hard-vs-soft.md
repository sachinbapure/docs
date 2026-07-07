# Source: https://mailvalid.io/blog/advanced-email-bounce-classification-hard-vs-soft

## Every Bounce Type Costs You Differently — Here's How to Stop Them All

Not all email bounces are created equal. A hard bounce destroys your sender reputation permanently. A soft bounce is a warning shot. And catch-all bounces? They're invisible assassins that look like deliveries but never reach a human inbox.

If you're running cold outreach, newsletters, or any high-volume email program, understanding bounce classification isn't optional — it's survival. ESPs track your bounce rate by type, and each category triggers different penalties.

**[Cut your bounce rate by 90% with MailValid → 100 free credits daily](https://mailvalid.io/register)**

This guide breaks down hard bounces, soft bounces, and catch-all traps. You'll learn the exact SMTP response codes that identify each type, how to diagnose your bounce rate, and how to integrate MailValid's email verification API to prevent bounces before they happen — all for $0.001 per email.

Ready to verify your email list?

100 free credits. $0.001/email after. No credit card required.

[Start Free](https://mailvalid.io/register)

## Hard Bounces: Permanent Delivery Failures

A hard bounce means the email address is permanently unreachable. The mailbox doesn't exist, the domain is dead, or the server explicitly rejects the recipient.

### Common Hard Bounce SMTP Codes

| Code | Meaning | Why It Happens |
| --- | --- | --- |
| 550 | Mailbox unavailable | Address doesn't exist |
| 551 | User not local | Domain exists, user doesn't |
| 552 | Mailbox full (permanent) | Rare — usually soft, but some servers treat as hard |
| 553 | Mailbox name not allowed | Invalid format or restricted |
| 554 | Transaction failed | Blacklisted or policy rejection |

### The Reputation Impact

Hard bounces are the most damaging bounce type because they signal **list quality problems**. Gmail, Outlook, and Yahoo track your hard bounce rate per campaign. Exceed 2% hard bounces and you enter the danger zone. Exceed 5% and you're likely throttled or blacklisted.

**The fix is simple:** Never send to an unverified address. MailValid's SMTP verification catches hard bounces before your message ever leaves your server.

## Soft Bounces: Temporary Delivery Failures

Soft bounces indicate a temporary problem. The address might be valid, but the server can't accept mail right now.

### Common Soft Bounce SMTP Codes

| Code | Meaning | Typical Cause |
| --- | --- | --- |
| 421 | Service not available | Server overload or maintenance |
| 450 | Mailbox unavailable | Often greylisting — try again later |
| 451 | Local error in processing | Server-side temporary issue |
| 452 | Insufficient system storage | Mailbox full |
| 4xx | Various temporary failures | Greylisting, rate limiting, DNS timeout |

### When Soft Bounces Become Hard Bounces

The danger with soft bounces is that they degrade over time. A "mailbox full" address eventually gets deactivated by the provider. A greylisted server might permanently block you if you retry too aggressively.

Best practice: Retry soft bounces 2-3 times over 72 hours, then suppress the address if it still fails. For cold email, it's safer to remove soft-bouncing addresses entirely — your reputation is worth more than one additional contact.

## Catch-All Bounces: The Invisible Killer

Catch-all domains are the most insidious bounce type because they **don't bounce at all** — at least not in the traditional sense.

A catch-all server accepts every email sent to its domain, regardless of whether the specific mailbox exists. Your ESP reports "delivered," but the message vanishes into a black hole. No reply. No open. No bounce notification.

### How Catch-All Domains Destroy Your Metrics

| Metric | Real Address | Catch-All Domain |
| --- | --- | --- |
| Delivery status | Delivered | Delivered (false positive) |
| Open rate | Trackable | 0% |
| Reply rate | Trackable | 0% |
| Bounce rate | Accurate | Underreported |
| Reputation impact | Normal | Hidden decay |

You're essentially sending emails into /dev/null while your metrics look healthy. Over time, ESPs notice the zero engagement from these "delivered" emails and penalize your domain anyway.

### Detecting Catch-All Domains

Standard regex or MX checks can't catch catch-alls. You need SMTP-level probing: attempting delivery to a **randomized, guaranteed-nonexistent address** at the same domain. If the server accepts it, the domain is catch-all.

**MailValid automatically detects catch-all domains during verification and flags them in the API response.**

## Bounce Rate Benchmarks by Email Type

| Bounce Type | Acceptable | Warning | Critical |
| --- | --- | --- | --- |
| Hard bounce | 0-1% | 1-2% | 2%+ |
| Soft bounce | 0-3% | 3-5% | 5%+ |
| Catch-all "delivered" | 0-5% | 5-10% | 10%+ |
| Overall bounce | 0-2% | 2-5% | 5%+ |

If your overall bounce rate exceeds 3%, pause all campaigns and clean your list immediately.

## How to Prevent Bounces with MailValid API

Prevention is cheaper than recovery. Here's how to wire MailValid into your email workflow.

### Python: Verify and Classify Before Sending

```python
import requests

API_KEY = "your_mailvalid_api_key"

def classify_email(email):
    """Verify an email and return bounce risk classification."""
    resp = requests.get(
        f"https://api.mailvalid.io/v1/verify?email={email}",
        headers={"Authorization": f"Bearer {API_KEY}"}
    )
    result = resp.json()

    if not result.get("valid"):
        return {"risk": "HIGH", "type": "hard_bounce", "reason": result.get("reason")}

    if result.get("catch_all"):
        return {"risk": "MEDIUM", "type": "catch_all", "reason": "Domain accepts all emails"}

    if result.get("disposable"):
        return {"risk": "HIGH", "type": "disposable", "reason": "Temporary email domain"}

    if result.get("role_based"):
        return {"risk": "LOW", "type": "role_based", "reason": "Generic address (info, sales, etc.)"}

    return {"risk": "LOW", "type": "verified", "reason": "Mailbox confirmed"}

# Example usage
emails = [
    "ceo@startup.com",
    "fake@nonexistent-domain.xyz",
    "admin@enterprise.com",
    "temp@10minutemail.com"
]

for email in emails:
    classification = classify_email(email)
    print(f"{email}: {classification['risk']} risk — {classification['type']}")
```

### JavaScript: Real-Time Bounce Prevention

```javascript
async function getBounceRisk(email) {
  const response = await fetch(
    `https://api.mailvalid.io/v1/verify?email=${encodeURIComponent(email)}`,
    { headers: { 'Authorization': 'Bearer YOUR_API_KEY' } }
  );
  const data = await response.json();

  if (!data.valid) return { send: false, reason: data.reason, risk: 'hard_bounce' };
  if (data.catch_all) return { send: false, reason: 'catch_all', risk: 'hidden_bounce' };
  if (data.disposable) return { send: false, reason: 'disposable', risk: 'hard_bounce' };
  return { send: true, risk: 'low' };
}

// Block high-risk addresses before they enter your sequence
const risk = await getBounceRisk('user@company.com');
if (!risk.send) {
  console.log(`Block: ${risk.reason} (${risk.risk})`);
}
```

### Bulk Verification with Bounce Classification

```python
import requests
import csv

API_KEY = "your_mailvalid_api_key"

def verify_and_classify_csv(input_path, output_path):
    with open(input_path) as f, open(output_path, 'w', newline='') as out:
        reader = csv.DictReader(f)
        fieldnames = reader.fieldnames + ['bounce_risk', 'bounce_type', 'mailvalid_verified']
        writer = csv.DictWriter(out, fieldnames=fieldnames)
        writer.writeheader()

        for row in reader:
            email = row['email']
            resp = requests.get(
                f"https://api.mailvalid.io/v1/verify?email={email}",
                headers={"Authorization": f"Bearer {API_KEY}"}
            )
            result = resp.json()

            if not result.get('valid'):
                row['bounce_risk'] = 'HIGH'
                row['bounce_type'] = 'hard_bounce'
            elif result.get('catch_all'):
                row['bounce_risk'] = 'MEDIUM'
                row['bounce_type'] = 'catch_all'
            elif result.get('disposable'):
                row['bounce_risk'] = 'HIGH'
                row['bounce_type'] = 'disposable'
            else:
                row['bounce_risk'] = 'LOW'
                row['bounce_type'] = 'verified'

            row['mailvalid_verified'] = result.get('valid', False)
            writer.writerow(row)

verify_and_classify_csv('list.csv', 'list_classified.csv')
```

## MailValid vs ZeroBounce vs NeverBounce: Bounce Detection Comparison

| Feature | MailValid | ZeroBounce | NeverBounce |
| --- | --- | --- | --- |
| Hard bounce prevention | ✅ Yes | ✅ Yes | ✅ Yes |
| Soft bounce flagging | ✅ Yes | ✅ Yes | ✅ Yes |
| Catch-all detection | ✅ Yes | ✅ Yes | ✅ Yes |
| Disposable detection | ✅ Yes | ✅ Yes | ✅ Yes |
| Role-based detection | ✅ Yes | ✅ Yes | ✅ Yes |
| Price per verification | **$0.001** | $0.008 | $0.007 |
| Free tier | **100/day** | 100 one-time | 1,000 one-time |

All three providers use the same core SMTP technology to detect bounces. MailValid delivers identical protection at **7-8x lower cost**.

## Building a Bounce-Resistant Email Workflow

Here's the exact workflow we recommend for teams sending 10,000+ emails monthly:

### 1\. Verify at Point of Capture

Use the JavaScript snippet above to validate emails in real-time as users type them into signup forms or lead capture pages.

### 2\. Pre-Send Bulk Verification

Run every list through MailValid's API 24 hours before sending. Remove all HIGH and MEDIUM risk addresses.

### 3\. Segment by Risk Level

- **LOW risk:** Primary sending sequence
- **MEDIUM risk (catch-all):** Separate test sequence with low volume
- **HIGH risk:** Suppressed permanently

### 4\. Monitor Post-Send Bounces

Even verified lists generate 1-2% soft bounces. Monitor your ESP's bounce reports and suppress repeat offenders.

### 5\. Re-verify Quarterly

Email lists decay 22-30% per year. Re-verify your entire database every 90 days.

## FAQ: Email Bounce Classification

### What's worse: hard bounce or soft bounce?

Hard bounces are worse for reputation because they prove you're sending to non-existent addresses. However, a high soft bounce rate also signals poor list hygiene.

### Can I fix a hard bounce?

No. Hard bounces are permanent. The only fix is removing the address from your list and preventing similar addresses from entering in the future.

### How does MailValid detect catch-all domains?

MailValid attempts verification on a randomized address at the target domain. If the server accepts it, the domain is flagged as catch-all.

### Is $0.001 per email enough to prevent bounces?

Yes. MailValid uses the same SMTP verification technology as $0.008 providers. The price difference is overhead, not accuracy.

### Can I try MailValid for free?

Yes — 100 free verification credits per day, no credit card required. [Start here →](https://mailvalid.io/register)

## Stop Guessing, Start Verifying

Bounce classification isn't academic — it's the difference between a healthy sender reputation and a blacklisted domain. Hard bounces, soft bounces, and catch-all traps each require different responses, but they all share one prevention strategy: **verify before you send.**

MailValid makes that prevention trivial. At $0.001 per email with 100 free daily credits, you can eliminate the vast majority of bounces without eating into your marketing budget.

**[Get 100 free credits and classify your list risk today →](https://mailvalid.io/register)**

**[See how MailValid pricing beats ZeroBounce and NeverBounce →](https://mailvalid.io/pricing)**

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