# Source: https://mailvalid.io/blog/email-verification-api-complete-developer-integration-guide

## The Problem: Your Signup Form Is Bleeding Money

Every fake email that slips through your signup form costs you **three ways**:

1. **Direct cost**: You're paying your ESP (SendGrid, Mailgun, AWS SES) to send to addresses that bounce. At $0.001 per email sent, a list with 25% invalid addresses wastes 25% of your email budget.
2. **Reputation cost**: Bounce rates above 2% trigger spam filters at Gmail, Outlook, and Yahoo. Once your domain reputation drops, even valid subscribers stop seeing your emails.
3. **Data cost**: Fake users pollute your analytics, inflate your MAU numbers, and make cohort analysis meaningless.

The solution is dead simple: verify every email **before** it enters your database. But most developers either skip verification entirely or implement a naive regex check that catches only obvious typos.

**This guide shows you how to integrate a real email verification API in under 10 minutes** — with production-ready code, caching, retries, and webhook handling. We'll compare MailValid ($0.001/email, 100 free credits) against ZeroBounce ($0.008/email) and NeverBounce ($0.007/email) so you can choose the right provider for your volume.

---

Ready to verify your email list?

100 free credits. $0.001/email after. No credit card required.

[Start Free](https://mailvalid.io/register)

## MailValid vs ZeroBounce vs NeverBounce: Developer Comparison

| Feature | MailValid | ZeroBounce | NeverBounce |
| --- | --- | --- | --- |
| **Price per email** | **$0.001** | $0.008 | $0.007 |
| **Free tier** | **100/day** | 100/month | None |
| **API response time** | < 200ms | < 300ms | < 400ms |
| **Bulk endpoint** | Yes (CSV/JSON) | Yes | Yes |
| **Webhook support** | Yes | Yes | Yes |
| **Disposable detection** | Yes (10,000+ domains) | Yes | Yes |
| **Catch-all detection** | Yes | Yes | Yes |
| **Role-based detection** | Yes | Yes | Yes |
| **Sandbox/test mode** | Yes | Yes | No |
| **SLA uptime** | 99.9% | 99.9% | 99.5% |

**The math is simple**: At 100,000 verifications per month, MailValid costs **$100**. ZeroBounce costs **$800**. NeverBounce costs **$700**. That's **$7,200-$8,400 saved per year** — enough to hire a junior developer.

[Start with 100 free verifications →](https://mailvalid.io/register)

---

## Quick Start: Verify an Email in 10 Lines of Code

MailValid's API uses a single endpoint for all verification types. No complicated tiering — one call gives you syntax, MX, SMTP, disposable, catch-all, and role-based checks.

### Python

```python
import requests

API_KEY = "your_mailvalid_api_key"
EMAIL = "user@example.com"

response = requests.post(
    "https://api.mailvalid.io/v1/verify",
    headers={
        "X-API-Key": API_KEY,
        "Content-Type": "application/json"
    },
    json={"email": EMAIL},
    timeout=10
)

result = response.json()
print(f"Status: {result['status']}")       # deliverable, undeliverable, risky, unknown
print(f"Score: {result['score']}")         # 0.0 to 1.0
print(f"Disposable: {result['is_disposable']}")
print(f"Free provider: {result['is_free_provider']}")
```

### Node.js

```javascript
const response = await fetch('https://api.mailvalid.io/v1/verify', {
  method: 'POST',
  headers: {
    'X-API-Key': process.env.MAILVALID_API_KEY,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ email: 'user@example.com' })
});

const result = await response.json();
console.log(result.status);        // deliverable | undeliverable | risky | unknown
console.log(result.score);         // 0.0 - 1.0 confidence
```

### PHP

```php
<?php
$ch = curl_init('https://api.mailvalid.io/v1/verify');
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode(['email' => 'user@example.com']));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    'X-API-Key: ' . $apiKey,
    'Content-Type: application/json'
]);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

$result = json_decode(curl_exec($ch), true);
echo $result['status'];   // deliverable | undeliverable | risky | unknown
echo $result['score'];    // 0.0 - 1.0
?>
```

### Go

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "net/http"
)

type VerifyRequest struct {
    Email string `json:"email"`
}

type VerifyResponse struct {
    Status       string  `json:"status"`
    Score        float64 `json:"score"`
    IsDisposable bool    `json:"is_disposable"`
}

func main() {
    payload, _ := json.Marshal(VerifyRequest{Email: "user@example.com"})
    req, _ := http.NewRequest("POST", "https://api.mailvalid.io/v1/verify", bytes.NewBuffer(payload))
    req.Header.Set("X-API-Key", apiKey)
    req.Header.Set("Content-Type", "application/json")

    client := &http.Client{Timeout: 10 * time.Second}
    resp, _ := client.Do(req)
    defer resp.Body.Close()

    var result VerifyResponse
    json.NewDecoder(resp.Body).Decode(&result)
    fmt.Printf("Status: %s, Score: %.2f\n", result.Status, result.Score)
}
```

### cURL

```bash
curl -X POST https://api.mailvalid.io/v1/verify \
  -H "X-API-Key: your_api_key" \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com"}'
```

**Sample response:**

```json
{
  "email": "user@example.com",
  "status": "deliverable",
  "score": 0.95,
  "is_disposable": false,
  "is_free_provider": true,
  "is_role_based": false,
  "is_catch_all": false,
  "mx_records": ["mail.example.com"],
  "smtp_check": "connect_success",
  "verified_at": "2026-04-21T10:00:00Z"
}
```

---

## Production Integration: Signup Form Pipeline

The 10-line examples above are fine for testing. In production, you need caching, retries, and graceful degradation. Here's a complete Python pipeline used by teams processing 1M+ signups per month.

### Step 1: Pre-Validation (Client-Side)

Don't waste API calls on obviously invalid emails. Check syntax in the browser first.

```javascript
function preValidate(email) {
  const pattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!pattern.test(email) || email.length > 254) {
    return { valid: false, reason: 'invalid_syntax' };
  }
  const [local] = email.split('@');
  if (local.length > 64) {
    return { valid: false, reason: 'local_part_too_long' };
  }
  return { valid: true };
}
```

### Step 2: Server-Side Verification with Caching

Cache results for 24 hours to avoid re-verifying the same email repeatedly.

```python
import requests
import hashlib
import json
import redis
from datetime import timedelta

class EmailVerifier:
    def __init__(self, api_key: str, cache_ttl: int = 86400):
        self.api_key = api_key
        self.cache_ttl = cache_ttl
        self.cache = redis.Redis(host='localhost', port=6379, db=0, decode_responses=True)
        self.base_url = "https://api.mailvalid.io/v1"

    def _cache_key(self, email: str) -> str:
        return f"ev:{hashlib.sha256(email.lower().encode()).hexdigest()[:16]}"

    def verify(self, email: str, fresh: bool = False) -> dict:
        email = email.lower().strip()
        cache_key = self._cache_key(email)

        if not fresh:
            cached = self.cache.get(cache_key)
            if cached:
                return json.loads(cached)

        response = requests.post(
            f"{self.base_url}/verify",
            headers={
                "X-API-Key": self.api_key,
                "Content-Type": "application/json"
            },
            json={"email": email},
            timeout=10
        )

        if response.status_code == 429:
            raise Exception("Rate limit exceeded — implement exponential backoff")

        result = response.json()
        self.cache.setex(cache_key, self.cache_ttl, json.dumps(result))
        return result

    def should_accept(self, email: str) -> tuple[bool, str]:
        result = self.verify(email)

        if result["status"] == "undeliverable":
            return False, "invalid_email"
        if result["is_disposable"]:
            return False, "disposable_email"
        if result["score"] < 0.5:
            return False, "low_confidence"

        return True, "accepted"
```

### Step 3: Retry Logic with Exponential Backoff

Network failures happen. Don't lose a signup because of a transient error.

```python
import time
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

def create_session(api_key: str):
    session = requests.Session()
    session.headers.update({"X-API-Key": api_key})

    retry = Retry(
        total=5,
        backoff_factor=1,
        status_forcelist=[429, 500, 502, 503, 504],
        allowed_methods=["POST"]
    )
    adapter = HTTPAdapter(max_retries=retry)
    session.mount("https://", adapter)
    return session
```

### Step 4: Real-Time Form Validation (JavaScript)

Verify emails as users type — without hitting rate limits.

```javascript
class EmailVerifier {
  constructor(apiKey) {
    this.apiKey = apiKey;
    this.timer = null;
    this.cache = new Map();
  }

  async verify(email, onResult) {
    clearTimeout(this.timer);

    if (!this.preValidate(email)) {
      onResult({ status: 'invalid', reason: 'syntax_error' });
      return;
    }

    if (this.cache.has(email)) {
      onResult(this.cache.get(email));
      return;
    }

    this.timer = setTimeout(async () => {
      try {
        const res = await fetch('https://api.mailvalid.io/v1/verify', {
          method: 'POST',
          headers: {
            'X-API-Key': this.apiKey,
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ email })
        });
        const result = await res.json();
        this.cache.set(email, result);
        setTimeout(() => this.cache.delete(email), 5 * 60 * 1000); // 5 min cache
        onResult(result);
      } catch (err) {
        onResult({ status: 'unknown', reason: 'api_error' });
      }
    }, 500); // 500ms debounce
  }

  preValidate(email) {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email) && email.length <= 254;
  }
}

// Usage
const verifier = new EmailVerifier('your_api_key');
document.getElementById('email').addEventListener('blur', (e) => {
  verifier.verify(e.target.value, (result) => {
    if (result.status === 'deliverable') {
      showSuccess('Valid email address');
    } else if (result.status === 'risky') {
      showWarning('This email may not receive messages');
    } else {
      showError('Please enter a valid email address');
    }
  });
});
```

---

## Bulk Verification: Clean 10,000 Emails in One Call

For list cleaning before campaigns, use the bulk endpoint instead of verifying one by one.

```python
import requests

# Submit a batch job
response = requests.post(
    "https://api.mailvalid.io/v1/bulk",
    headers={"X-API-Key": api_key, "Content-Type": "application/json"},
    json={
        "emails": ["user1@example.com", "user2@test.com", "..."],  # up to 10,000
        "webhook_url": "https://yourapp.com/webhooks/verification"
    }
)

job = response.json()
print(f"Job ID: {job['job_id']}, Status: {job['status']}")

# Poll for results
while True:
    status = requests.get(
        f"https://api.mailvalid.io/v1/bulk/{job['job_id']}",
        headers={"X-API-Key": api_key}
    ).json()

    if status["status"] == "completed":
        deliverable = [r for r in status["results"] if r["status"] == "deliverable"]
        rejected = [r for r in status["results"] if r["status"] == "undeliverable"]
        print(f"Done: {len(deliverable)} valid, {len(rejected)} invalid")
        break

    time.sleep(5)
```

**Cost**: 10,000 emails × $0.001 = **$10**. ZeroBounce charges **$80** for the same volume.

---

## Webhook Integration: Get Results Without Polling

For large batches, set up a webhook to receive results asynchronously.

```python
from flask import Flask, request, jsonify
import hmac
import hashlib

app = Flask(__name__)
WEBHOOK_SECRET = "your_webhook_secret"

@app.route('/webhooks/verification', methods=['POST'])
def handle_webhook():
    # Verify signature
    signature = request.headers.get('X-Webhook-Signature', '')
    expected = hmac.new(
        WEBHOOK_SECRET.encode(),
        request.get_data(),
        hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(f"sha256={expected}", signature):
        return "Invalid signature", 401

    payload = request.json
    if payload["status"] == "completed":
        for result in payload["results"]:
            if result["status"] == "undeliverable":
                add_to_suppression_list(result["email"])
            elif result["status"] == "deliverable":
                mark_as_verified(result["email"])

    return jsonify({"received": True}), 200
```

---

## Common Integration Mistakes (And How to Avoid Them)

### Mistake 1: Verifying at Send Time Instead of Capture Time

**Wrong**: Verify every email right before sending a campaign. **Right**: Verify once at signup, then cache the result for 90 days. Only re-verify if the email bounces.

### Mistake 2: Blocking All "Risky" Emails

A "risky" email isn't always bad. Catch-all domains (like small business servers) accept all mail — these are often legitimate. Flag them for monitoring rather than auto-rejecting.

```python
if result["status"] == "risky":
    # Allow signup but flag for review
    user.flags.append("risky_email")
    user.save()
```

### Mistake 3: Ignoring Role Accounts

Role accounts (`info@`, `support@`, `admin@`) are shared mailboxes with low engagement. Filter them out for cold email campaigns, but they're fine for transactional emails.

```python
if result["is_role_based"] and campaign_type == "cold_outreach":
    return False, "role_based_address"
```

### Mistake 4: No Fallback on API Failure

If your verification API goes down, your signup form shouldn't break. Implement a fail-open strategy:

```python
try:
    result = verifier.verify(email)
except Exception:
    # API down — allow signup but mark for later review
    user.flags.append("unverified_email")
    return True, "api_unavailable"
```

---

## Pricing at Scale: What 1 Million Verifications Cost

| Provider | 1K emails | 10K emails | 100K emails | 1M emails |
| --- | --- | --- | --- | --- |
| **MailValid** | **$1** | **$10** | **$100** | **$900** |
| ZeroBounce | $8 | $80 | $650 | $4,500 |
| NeverBounce | $7 | $70 | $550 | $3,500 |
| Hunter | $34 | $340 | — | — |
| Kickbox | $10 | $100 | $800 | $4,000 |

**At 100K verifications/month, MailValid saves you $450–$700 every month.** That's $5,400–$8,400 per year.

[See full pricing →](https://mailvalid.io/pricing)

---

## Conclusion: Start Verifying Today

Email verification isn't optional infrastructure — it's profit protection. Every invalid email you block at signup saves you ESP costs, protects your sender reputation, and keeps your analytics clean.

**MailValid is the fastest, cheapest way to get started:**

- **$0.001 per email** (5–8× cheaper than competitors)
- **100 free verifications/day** (no credit card required)
- **Single endpoint** — one call gets you syntax, MX, SMTP, disposable, catch-all, and role-based detection
- **Sub-200ms response time** globally
- **99.9% uptime SLA**

**Get your free API key in 30 seconds:**

```bash
curl -X POST https://api.mailvalid.io/v1/verify \
  -H "X-API-Key: your_free_key" \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com"}'
```

[Get 100 free credits →](https://mailvalid.io/register)

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