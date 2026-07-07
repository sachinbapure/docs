# Source: https://mailvalid.io/blog/email-verification-api-signup-form-integration

## Why Real-Time Email Verification at Signup Is a Game Changer

Every day, thousands of applications quietly accumulate garbage in their user databases. Mistyped addresses, throwaway emails, role-based accounts, and outright fake signups pile up silently — and by the time you notice the damage, your deliverability is already suffering, your marketing metrics are skewed, and your support team is chasing ghosts.

The traditional answer has been **batch verification**: export your list periodically, run it through a verification service, and clean up the mess after the fact. Batch verification has its place, but it is fundamentally reactive. You are mopping the floor after the tap has been left running.

Real-time verification at the point of signup is the tap. It stops bad data from entering your system in the first place, and the downstream benefits compound over time.

According to a study by Experian, **91% of companies report that data quality issues directly impact their revenue**, with inaccurate contact data being one of the leading causes. Meanwhile, research from HubSpot indicates that **email lists decay at approximately 22.5% per year** through natural churn alone — meaning you cannot afford to start with a polluted list and hope to clean it later. And the Radicati Group estimates that **over 45% of all email traffic is spam or invalid**, which means a significant portion of addresses submitted to any open signup form will be worthless without a verification layer.

Real-time verification solves all three problems simultaneously. It catches typos before they become permanent records. It blocks disposable email addresses before they inflate your subscriber count. It filters out role-based addresses like `admin@` or `noreply@` that almost never represent genuine users. And it does all of this in milliseconds, invisibly, without friction for legitimate users.

This guide walks you through building a complete, production-ready signup form with real-time verification using the MailValid API — from the HTML and JavaScript on the frontend to the backend middleware in Python and Node.js, including edge case handling that separates a toy implementation from one you can actually trust in production.

---

Ready to verify your email list?

100 free credits. $0.001/email after. No credit card required.

[Start Free](https://mailvalid.io/register)

## UX Best Practices: When and How to Validate

Before writing a single line of code, it is worth thinking carefully about the user experience. Real-time verification can feel magical when done well, or infuriating when done poorly. The difference comes down to timing, messaging, and graceful degradation.

### Validate on Blur, Not on Keystroke

The single most important UX decision is **when to trigger validation**. There are three common approaches: on keystroke, on blur (when the user leaves the field), and on submit.

Validating on every keystroke is almost always wrong for email verification. The user is mid-input, the address is incomplete, and firing API calls against a half-typed string wastes credits, creates flickering error states, and feels invasive. Nobody wants to be told their email is invalid before they have finished typing it.

**Validating on blur** — when the input loses focus — strikes the right balance. The user has finished entering their address and moved to the next field. At that moment, a quick API call can return a result before they reach the submit button, giving them the opportunity to correct a typo without the friction of a failed form submission.

**Validating on submit** is the backstop. Even if your blur validation runs correctly, always re-validate on submit to catch cases where the user pastes an address and submits immediately, or where the blur validation was skipped.

### Write Error Messages That Help, Not Punish

Generic error messages like "Invalid email" are worse than useless — they tell the user something is wrong without helping them fix it. The MailValid API returns structured results that let you write genuinely helpful messages:

- **Syntax error**: "That doesn't look like a valid email address — check for typos like missing `@` or `.`"
- **Domain does not exist**: "We couldn't find that email domain. Did you mean `gmail.com` instead of `gmai.com`?"
- **Disposable email**: "We don't accept temporary email addresses. Please use your regular email."
- **Mailbox does not exist**: "That email address doesn't appear to exist. Double-check the spelling."
- **Role-based address**: "Please use a personal email address rather than a shared inbox like `info@` or `admin@`."

Notice that each message is specific, non-accusatory, and actionable. Users who make genuine typos will appreciate the guidance. Users trying to game your system with fake addresses will be stopped cleanly.

### Visual Feedback States

Your form should have four distinct visual states for the email field:

- **Neutral**: No validation has run yet (initial state)
- **Validating**: API call in progress — show a spinner or subtle animation
- **Valid**: Green checkmark, positive reinforcement
- **Invalid**: Red border with specific error message

Avoid blocking the submit button during the validation API call. If the call takes longer than expected, the user should still be able to attempt submission — your backend will catch it regardless.

---

## Frontend Code: HTML Form with JavaScript Validation

Here is a complete frontend implementation. The HTML provides the structure, and the JavaScript handles the blur event, calls the MailValid API through a lightweight backend proxy (more on why you need the proxy below), and updates the UI accordingly.

**Important**: Never call the MailValid API directly from the browser. Your API key would be exposed in the network tab to anyone who opens developer tools. Always route verification calls through a backend proxy endpoint — which is exactly what `/api/verify-email` represents above.

---

## Backend Code: Middleware in Python and Node.js

Your backend is the authoritative layer. Even if frontend validation is bypassed (and determined users will bypass it), the backend should independently verify every email before creating a user record.

### Python Flask Implementation

```python
import os
import time
import requests
from flask import Flask, request, jsonify
from functools import wraps

app = Flask(__name__)

MAILVALID_API_KEY = os.environ.get('MAILVALID_API_KEY')
MAILVALID_API_URL = 'https://api.mailvalid.io/v1/verify'
VERIFICATION_TIMEOUT = 5  # seconds
MAX_RETRIES = 2

def verify_email_with_retry(email, retries=MAX_RETRIES):
    """
    Calls the MailValid API with retry logic and timeout handling.
    Returns the parsed JSON result or raises an exception.
    """
    for attempt in range(retries + 1):
        try:
            response = requests.get(
                MAILVALID_API_URL,
                params={'email': email},
                headers={'Authorization': f'Bearer {MAILVALID_API_KEY}'},
                timeout=VERIFICATION_TIMEOUT
            )
            response.raise_for_status()
            return response.json()
        except requests.exceptions.Timeout:
            if attempt < retries:
                time.sleep(0.5 * (attempt + 1))  # Exponential backoff
                continue
            raise
        except requests.exceptions.HTTPError as e:
            if e.response.status_code == 429:
                # Rate limited — wait and retry
                retry_after = int(e.response.headers.get('Retry-After', 2))
                time.sleep(retry_after)
                continue
            raise
        except requests.exceptions.RequestException:
            if attempt < retries:
                time.sleep(0.5 * (attempt + 1))
                continue
            raise

def should_reject_email(result):
    """
    Determines whether to reject a signup based on verification result.
    Returns (reject: bool, reason: str)
    """
    if result.get('is_disposable'):
        return True, "We don't accept temporary email addresses."
    if result.get('is_role_based'):
        return True, "Please use a personal email address."
    if result.get('status') == 'invalid':
        sub = result.get('sub_status', '')
        if sub == 'mailbox_not_found':
            return True, "That email address doesn't appear to exist."
        if sub == 'domain_not_found':
            return True, "We couldn't find that email domain."
        return True, "Please enter a valid email address."
    # 'valid', 'unknown', 'catch_all' — accept
    return False, None

# Proxy endpoint for frontend verification calls
@app.route('/api/verify-email', methods=['POST'])
def proxy_verify_email():
    data = request.get_json()
    email = data.get('email', '').strip()

    if not email:
        return jsonify({'error': 'Email is required'}), 400

    try:
        result = verify_email_with_retry(email)
        # Return a sanitized subset — don't expose full API response to frontend
        return jsonify({
            'status': result.get('status'),
            'sub_status': result.get('sub_status'),
            'is_disposable': result.get('is_disposable', False),
            'is_role_based': result.get('is_role_based', False),
        })
    except Exception as e:
        app.logger.error(f'Email verification proxy error: {e}')
        # Fail open — return unknown status so frontend doesn't block user
        return jsonify({'status': 'unknown'})

# Main signup endpoint
@app.route('/api/signup', methods=['POST'])
def signup():
    data = request.get_json()
    email = data.get('email', '').strip().lower()

    if not email:
        return jsonify({'success': False, 'message': 'Email is required'}), 400

    # Server-side verification — always run this regardless of frontend state
    fail_open = True  # Change to False if you want strict mode
    should_reject = False
    rejection_reason = None
    flagged = False

    try:
        result = verify_email_with_retry(email)
        should_reject, rejection_reason = should_reject_email(result)

        # Flag catch_all and unknown for manual review
        if result.get('status') in ('catch_all', 'unknown'):
            flagged = True

    except Exception as e:
        app.logger.error(f'Email verification failed for {email}: {e}')
        if not fail_open:
            return jsonify({
                'success': False,
                'message': 'We could not verify your email address. Please try again.'
            }), 503

    if should_reject:
        return jsonify({'success': False, 'message': rejection_reason}), 422

    # Create the user — pass flagged status to your user creation logic
    try:
        user = create_user(email=email, requires_review=flagged)
        return jsonify({'success': True, 'user_id': str(user.id)})
    except Exception as e:
        app.logger.error(f'User creation failed: {e}')
        return jsonify({'success': False, 'message': 'Signup failed. Please try again.'}), 500

def create_user(email, requires_review=False):
    # Replace with your actual user creation logic (SQLAlchemy, etc.)
    pass

if __name__ == '__main__':
    app.run(debug=False)
```

### Node.js Express Implementation

```javascript
const express = require('express');
const axios = require('axios');

const app = express();
app.use(express.json());

const MAILVALID_API_KEY = process.env.MAILVALID_API_KEY;
const MAILVALID_API_URL = 'https://api.mailvalid.io/v1/verify';
const VERIFICATION_TIMEOUT = 5000; // ms
const MAX_RETRIES = 2;

async function verifyEmailWithRetry(email, retriesLeft = MAX_RETRIES) {
  try {
    const response = await axios.get(MAILVALID_API_URL, {
      params: { email },
      headers: { Authorization: `Bearer ${MAILVALID_API_KEY}` },
      timeout: VERIFICATION_TIMEOUT,
    });
    return response.data;
  } catch (err) {
    if (err.response && err.response.status === 429) {
      const retryAfter = parseInt(err.response.headers['retry-after'] || '2', 10);
      await new Promise((resolve) => setTimeout(resolve, retryAfter * 1000));
      if (retriesLeft > 0) return verifyEmailWithRetry(email, retriesLeft - 1);
    }
    if ((err.code === 'ECONNABORTED' || err.code === 'ETIMEDOUT') && retriesLeft > 0) {
      await new Promise((resolve) => setTimeout(resolve, 500 * (MAX_RETRIES - retriesLeft + 1)));
      return verifyEmailWithRetry(email, retriesLeft - 1);
    }
    throw err;
  }
}

function shouldRejectEmail(result) {
  if (result.is_disposable) return { reject: true, reason: "We don't accept temporary email addresses." };
  if (result.is_role_based) return { reject: true, reason: 'Please use a personal email address.' };
  if (result.status === 'invalid') {
    const sub = result.sub_status || '';
    if (sub === 'mailbox_not_found') return { reject: true, reason: "That email address doesn't appear to exist." };
    if (sub === 'domain_not_found') return { reject: true, reason: "We couldn't find that email domain." };
    return { reject: true, reason: 'Please enter a valid email address.' };
  }
  return { reject: false, reason: null };
}

// Proxy endpoint for frontend
app.post('/api/verify-email', async (req, res) => {
  const { email } = req.body;
  if (!email) return res.status(400).json({ error: 'Email is required' });

  try {
    const result = await verifyEmailWithRetry(email.trim());
    return res.json({
      status: result.status,
      sub_status: result.sub_status,
      is_disposable: result.is_disposable || false,
      is_role_based: result.is_role_based || false,
    });
  } catch (err) {
    console.error('Verification proxy error:', err.message);
    return res.json({ status: 'unknown' });
  }
});

// Main signup endpoint
app.post('/api/signup', async (req, res) => {
  const email = (req.body.email || '').trim().toLowerCase();
  if (!email) return res.status(400).json({ success: false, message: 'Email is required' });

  const FAIL_OPEN = true;
  let flagged = false;

  try {
    const result = await verifyEmailWithRetry(email);
    const { reject, reason } = shouldRejectEmail(result);

    if (reject) return res.status(422).json({ success: false, message: reason });
    if (['catch_all', 'unknown'].includes(result.status)) flagged = true;

  } catch (err) {
    console.error(`Verification failed for ${email}:`, err.message);
    if (!FAIL_OPEN) {
      return res.status(503).json({
        success: false,
        message: 'Could not verify your email. Please try again.',
      });
    }
  }

  try {
    const user = await createUser({ email, requiresReview: flagged });
    return res.json({ success: true, userId: user.id });
  } catch (err) {
    console.error('User creation error:', err.message);
    return res.status(500).json({ success: false, message: 'Signup failed. Please try again.' });
  }
});

async function createUser({ email, requiresReview }) {
  // Replace with your actual database logic
  return { id: 'user_123' };
}

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## What to Do with the Validation Result: Accept, Reject, or Flag

Not every email falls neatly into "accept" or "reject." The MailValid API returns nuanced results that call for a three-tier response strategy.

### Tier 1: Reject Immediately

These results indicate the address is definitively unusable or undesirable:

- **`status: invalid`** — The address fails syntax checks, the domain does not exist, or the mailbox was confirmed not to exist
- **`is_disposable: true`** — The address belongs to a known disposable email provider
- **`is_role_based: true`** — The address is a shared inbox (`admin@`, `info@`, `support@`, `noreply@`)

Reject these at the API layer and return a helpful error message. Do not create a user record.

### Tier 2: Accept Cleanly

- **`status: valid`** — The address passed all checks including SMTP verification

Accept these immediately and proceed with user creation.

### Tier 3: Flag for Review

These are the ambiguous cases that require a judgment call:

- **`status: catch_all`** — The mail server accepts all addresses, so individual mailbox existence cannot be confirmed
- **`status: unknown`** — The verification could not be completed conclusively

For most applications, the right approach is to **accept these addresses but flag the user record** with a `requires_email_confirmation` field or similar marker. Send a confirmation email and require the user to verify ownership before accessing key features. This is the standard double opt-in pattern, and it provides a natural second verification layer at zero additional cost.

---

## Handling Edge Cases: Timeouts, Rate Limits, and Retry Logic

Production systems encounter failure modes that never appear in tutorials. Here is how to handle the three most important ones.

### Timeout: Fail Open vs. Fail Closed

When the verification API call times out, you have two choices:

**Fail open**: Proceed as if the email is valid. The user is not blocked. You may accept some bad addresses, but you never block a legitimate user due to a network hiccup.

**Fail closed**: Reject the signup with a "try again later" message. No bad addresses get through, but legitimate users are blocked when your verification provider has downtime.

For most consumer applications, **fail open is the right default**. The cost of blocking a real user is higher than the cost of admitting one unverified address. Combine fail-open with double opt-in confirmation emails and you get the best of both worlds.

For high-risk applications (financial services, healthcare, anything where fake accounts cause serious harm), fail closed may be appropriate — but communicate clearly to the user that verification is temporarily unavailable and invite them to try again.

Set your timeout to **3–5 seconds**. Anything longer will noticeably degrade the signup experience.

### Rate Limits

The MailValid API returns HTTP 429 when you exceed your rate limit. Your retry logic should:

1. Read the `Retry-After` header if present
2. Wait the specified duration before retrying
3. Implement a maximum retry count (2–3 attempts is sufficient)
4. Fall back to fail-open if retries are exhausted

Both the Python and Node.js examples above implement this pattern. Importantly, if you are seeing 429s regularly, it is a signal to implement **caching** — store recent verification results keyed by email address with a short TTL (15–30 minutes). Most signup flows will never see the same address twice, but caching protects against users who click "back" and resubmit.

### Frontend Caching

Notice the `validationCache` object in the JavaScript example. This simple in-memory cache prevents redundant API calls when a user types an address, tabs away, then tabs back to the same field. It also prevents double-calls when your blur handler and submit handler both try to verify the same address. In production, this cache lives only for the duration of the page session, which is appropriate — you do not want stale verification results persisting across sessions.

---

## Full Working Integration: Putting It All Together

Here is a summary of the complete architecture and the key decisions that make it production-ready:

**Layer 1 — Client-side syntax check**: A simple regex runs instantly on blur, catching obvious malformations before any API call is made. This is free and fast.

**Layer 2 — Frontend API call via backend proxy**: The JavaScript calls your own `/api/verify-email` endpoint, which proxies to MailValid. Your API key never touches the browser. Results are cached in memory for the session.

**Layer 3 — Real-time UI feedback**: The form shows a spinner during verification, a green checkmark on success, and a specific, actionable error message on failure. The submit button is never permanently disabled — users can always attempt submission.

**Layer 4 — Backend verification middleware**: Every signup request runs through server-side verification independently of whatever the frontend reported. This is the authoritative layer. It cannot be bypassed.

**Layer 5 — Three-tier result handling**: Invalid, disposable, and role-based addresses are rejected with helpful messages. Valid addresses are accepted. Catch-all and unknown addresses are accepted but flagged for double opt-in confirmation.

**Layer 6 — Retry logic and graceful degradation**: Timeouts retry with backoff up to twice. Rate limit responses respect `Retry-After`. If all retries fail, the system fails open (configurable) rather than blocking the user.

**Layer 7 — Double opt-in as the final safety net**: Any address that passes verification but carries uncertainty (catch-all, unknown) triggers a confirmation email. This is standard practice and provides a human-verified signal that no automated system can fully replace.

This architecture means that even in the worst case — your verification provider is completely unreachable — your signup flow continues to function. You may accumulate a small number of unverified addresses during the outage, but your double opt-in flow will surface them before they cause any real damage.

The result is a signup form that your legitimate users will barely notice (verification takes milliseconds), while bad actors, bots, and careless typo-makers are stopped cleanly at the door. Your database stays clean, your deliverability stays high, and your marketing metrics actually mean something.

---

## Start Building Today

Real-time email verification is one of those rare investments that pays dividends indefinitely. Every clean record you add to your database compounds: better open rates, better deliverability, lower bounce rates, more accurate analytics, and fewer support headaches chasing users who never actually existed.

The implementation above is complete and production-ready. You can drop the Flask or Express middleware into an existing application in an afternoon, wire up the frontend JavaScript to your existing form, and start protecting your signup flow immediately.

The only thing left is your API key.

Ready to verify your list? [Get started free](https://mailvalid.io/pricing) — 1,000 credits free.

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