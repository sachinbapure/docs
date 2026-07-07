# Source: https://mailvalid.io/blog/mastering-email-deliverability-spf-dkim-dmarc

Your emails are being silently rejected, flagged as spam, or worse — spoofed by attackers — and you may not even know it. In 2024, over 45% of all email traffic is classified as spam, and inbox placement rates for legitimate senders have dropped to an average of 83% globally, according to Validity's 2024 Deliverability Benchmark Report. If you're a developer, system administrator, or email marketing professional responsible for email infrastructure, three DNS-based authentication standards — **SPF**, **DKIM**, and **DMARC** — are the difference between your messages landing in the inbox and disappearing into the void. This guide gives you everything: the technical depth, the configuration steps, the real DNS records, the code, and the monitoring strategies you need to master email deliverability once and for all.

---

## Why Email Authentication Is No Longer Optional: The Deliverability Crisis

The modern email ecosystem is under siege. Phishing attacks, business email compromise (BEC), and domain spoofing have forced inbox providers — Google, Microsoft, Yahoo, and others — to dramatically raise the bar for what they consider a trustworthy sender. The consequences for legitimate businesses are severe and often invisible: messages silently discarded, sender reputations permanently damaged, and customer relationships eroded without a single bounce notification.

### The Numbers That Should Keep You Awake at Night

According to Validity's 2024 Deliverability Benchmark Report, the average inbox placement rate across all industries sits at 83.1%, meaning roughly 1 in 6 legitimate emails never reaches its intended recipient. Litmus's 2023 State of Email report found that email generates an average ROI of $36 for every $1 spent — but that figure assumes your emails actually arrive. Return Path's research consistently shows that senders with proper authentication records achieve inbox placement rates 10–15 percentage points higher than those without.

Google and Yahoo's February 2024 sender requirements made authentication mandatory for bulk senders — defined as anyone sending more than 5,000 messages per day to Gmail addresses. Failure to implement SPF, DKIM, and DMARC now results in outright rejection with SMTP response codes like **550 5.7.26** ("This message does not have authentication information or fails to pass authentication checks"), a permanent failure that means your message is gone.

### What SMTP Error Codes Are Telling You

Understanding SMTP response codes (defined in RFC 5321 and enhanced status codes in RFC 3463) is foundational to diagnosing deliverability failures. When your email is rejected for authentication reasons, you'll encounter specific codes that tell you exactly what went wrong:

- **550 5.7.1** — Message rejected due to SPF policy failure. The sending IP is not authorized by the domain's SPF record.
- **550 5.7.26** — Authentication required; message fails DMARC policy evaluation.
- **421 4.7.0** — Temporary deferral due to reputation issues; often precedes permanent rejection.
- **450 4.7.1** — Temporary failure; recipient mail server is not accepting messages from your IP right now.
- **551 5.1.1** — User not local; the recipient address does not exist at this domain.
- **552 5.3.4** — Message size exceeds administrative limit; less common but relevant in bulk sending. These codes are not just error messages — they are diagnostic signals. A pattern of **421** deferrals transitioning to **550** rejections is a classic sign that your sender reputation has degraded to the point of blocklisting. Proper SPF, DKIM, and DMARC implementation directly prevents the authentication-related variants of these failures.

---

Ready to verify your email list?

100 free credits. $0.001/email after. No credit card required.

[Start Free](https://mailvalid.io/register)

## SPF (Sender Policy Framework): Authorizing Your Sending Infrastructure

The **Sender Policy Framework**, formally defined in **RFC 7208**, is a DNS-based authentication mechanism that allows domain owners to specify which mail servers are authorized to send email on behalf of their domain. It works at the envelope level — specifically, it validates the **MAIL FROM** (also called the Return-Path or envelope sender) against the domain's published SPF record during the SMTP transaction.

### How SPF Works: The Technical Mechanism

When a receiving mail server accepts an inbound SMTP connection, it performs the following SPF evaluation process:

- Extract the domain from the **MAIL FROM** command in the SMTP envelope (e.g., _bounce@mail.yourdomain.com_).
- Query the DNS TXT record for that domain (e.g., _mail.yourdomain.com_).
- Parse the SPF record and evaluate the sending IP address against the authorized mechanisms (ip4, ip6, a, mx, include, etc.).
- Return a result: **pass**, **fail**, **softfail**, **neutral**, **temperror**, or **permerror**.
- Apply the qualifier associated with the matched mechanism (or the default **~all** / **\-all** at the end). RFC 7208 specifies that SPF records must be published as DNS TXT records (previously SPF type records were used, but these were deprecated). The record must begin with the version tag **v=spf1**.

### Anatomy of a Real SPF Record

Here is a production-grade SPF record for a company using Google Workspace, a third-party ESP (Mailchimp), a transactional email provider (SendGrid), and their own on-premise mail server:

```python
; SPF TXT record for yourdomain.com
; Published at: yourdomain.com IN TXT
"v=spf1 ip4:203.0.113.10 ip4:198.51.100.0/24 include:_spf.google.com include:servers.mcsv.net include:sendgrid.net a:mail.yourdomain.com -all"
; Breakdown:
; v=spf1                    - SPF version 1 (required)
; ip4:203.0.113.10          - Authorize a specific IPv4 address (on-premise server)
; ip4:198.51.100.0/24       - Authorize an entire IPv4 CIDR range
; include:_spf.google.com   - Delegate to Google's SPF record (Google Workspace)
; include:servers.mcsv.net  - Delegate to Mailchimp's SPF record
; include:sendgrid.net      - Delegate to SendGrid's SPF record
; a:mail.yourdomain.com     - Authorize the A record of mail.yourdomain.com
; -all                      - Hard fail: reject all other senders (recommended)
; Note: ~all = softfail (mark as suspicious but accept)
; Note: ?all = neutral (no policy, not recommended)
```

### The 10-Lookup Limit: The Most Dangerous SPF Trap

RFC 7208, Section 4.6.4, imposes a critical constraint: SPF evaluation must not result in more than **10 DNS lookups**. Each **include**, **a**, **mx**, **ptr**, and **exists** mechanism triggers a DNS lookup. Exceeding this limit causes a **permerror** result, which many receiving servers treat as a hard fail — silently breaking your email authentication.

Common scenarios that push senders over the 10-lookup limit include using multiple ESPs (each with their own **include** chains), adding CDN or cloud provider ranges, and inheriting nested **include** records from third-party services. Tools like MXToolbox's SPF analyzer or dmarcian's SPF Surveyor can help you audit your lookup count before deployment.

### Common SPF Configuration Mistakes

- **Using ~all instead of -all in production:** Softfail tells receivers to accept but mark the message. Hard fail (-all) actively rejects unauthorized senders. Use -all once you've verified all legitimate sending sources.
- **Publishing multiple SPF records:** RFC 7208 is explicit — a domain must have exactly one SPF TXT record. Multiple records cause a permerror.
- **Forgetting subdomains:** SPF records are not inherited by subdomains. If you send from _mail.yourdomain.com_, that subdomain needs its own SPF record.
- **Not updating SPF when adding a new ESP:** Every new email service you add must be reflected in your SPF record before you start sending.

---

## DKIM (DomainKeys Identified Mail): Cryptographic Proof of Message Integrity

**DomainKeys Identified Mail, defined in RFC 6376, provides a cryptographic signature mechanism that allows receiving mail servers to verify that an email was genuinely sent by the domain it claims to be from, and that the message content was not altered in transit. Unlike SPF, which validates the envelope sender, DKIM validates the From header — the address users actually see in their email client.**

### How DKIM Works: Asymmetric Cryptography in Email

DKIM uses a public/private key pair. Here's the complete flow:

- The sending mail server generates an RSA or Ed25519 private key and publishes the corresponding public key as a DNS TXT record under a selector subdomain (e.g., _selector1.\_domainkey.yourdomain.com_).
- When sending an email, the mail server computes a cryptographic hash of specified email headers (From, To, Subject, Date, etc.) and the message body.
- The hash is signed with the private key, and the resulting signature is added to the email as a **DKIM-Signature** header.
- The receiving mail server extracts the domain and selector from the DKIM-Signature header, retrieves the public key from DNS, and uses it to verify the signature.
- If the signature is valid, the message content and headers have not been tampered with since signing.

### Anatomy of a Real DKIM DNS Record

```python
; DKIM public key DNS TXT record
; Published at: selector1._domainkey.yourdomain.com IN TXT
"v=DKIM1; k=rsa; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2a7..."
; Breakdown:
; v=DKIM1       - DKIM version (required)
; k=rsa         - Key type: RSA (most common) or ed25519 (modern, shorter)
; p=MIIBIj...   - Base64-encoded public key
; s=email       - Optional: service type restriction (email = only for email)
; t=s           - Optional: strict mode (subdomains cannot use this key)
; t=y           - Optional: testing mode (receivers should not reject failures)
; Example DKIM-Signature header added to outbound email:
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
d=yourdomain.com; s=selector1;
h=from:to:subject:date:message-id:content-type;
bh=47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=;
b=ABC123...base64encodedSignature...XYZ789
```

### Key Length, Algorithm Selection, and Rotation Strategy

RFC 6376 originally specified RSA with SHA-256 (**rsa-sha256**) as the primary algorithm. Modern implementations should use at minimum **2048-bit RSA keys** — 1024-bit keys are now considered cryptographically weak and are rejected by some receiving servers, including Gmail. For maximum security and performance, consider **Ed25519** keys, which provide equivalent security to 3072-bit RSA with much shorter key material.

DKIM key rotation is a security best practice that is frequently neglected. The recommended rotation interval is every 6–12 months. The selector-based architecture makes rotation straightforward: you publish a new key under a new selector (e.g., _selector2.\_domainkey.yourdomain.com_), configure your mail server to sign with the new key, allow DNS propagation, then retire the old selector.

### DKIM Canonicalization: relaxed/relaxed vs. simple/simple

RFC 6376 defines two canonicalization algorithms — **simple** and **relaxed** — that determine how headers and body are normalized before hashing. The **c=** tag in the DKIM-Signature header specifies header/body canonicalization respectively.

- **simple/simple:** Strict mode. Any whitespace change, header modification, or body alteration breaks the signature. Rarely used in production due to fragility.
- **relaxed/relaxed:** Tolerates minor whitespace changes and header folding. This is the standard choice for production environments, as email gateways and forwarding services often make minor whitespace modifications.
- **relaxed/simple:** Sometimes used when body integrity is paramount but header flexibility is needed.

### Configuring DKIM on Postfix with OpenDKIM

```python
# Production OpenDKIM configuration (/etc/opendkim.conf)
# Tested on Ubuntu 22.04 LTS with Postfix 3.6
# Core settings
Mode                    sv          # Sign (s) and verify (v) mode
Domain                  yourdomain.com
KeyFile                 /etc/opendkim/keys/yourdomain.com/selector1.private
Selector                selector1
Canonicalization        relaxed/relaxed
SignatureAlgorithm      rsa-sha256
# Socket for Postfix integration
Socket                  local:/var/spool/postfix/opendkim/opendkim.sock
# Logging
Syslog                  yes
SyslogSuccess           yes
LogWhy                  yes
# Signing table - maps sender addresses to signing configurations
SigningTable            refile:/etc/opendkim/signing.table
KeyTable                /etc/opendkim/key.table
InternalHosts           /etc/opendkim/trusted.hosts
# Security settings
RequireSafeKeys         yes
MinimumKeyBits          2048        # Reject keys shorter than 2048 bits
# /etc/opendkim/signing.table
# Format: sender_pattern    key_identifier
*@yourdomain.com            yourdomain.com
*@mail.yourdomain.com       yourdomain.com
# /etc/opendkim/key.table
# Format: key_identifier    domain:selector:keyfile
yourdomain.com    yourdomain.com:selector1:/etc/opendkim/keys/yourdomain.com/selector1.private
# Generate a new 2048-bit RSA DKIM key pair:
# opendkim-genkey -b 2048 -d yourdomain.com -s selector1 -D /etc/opendkim/keys/yourdomain.com/
# This creates: selector1.private (keep secret) and selector1.txt (publish in DNS)
```

---

## DMARC (Domain-based Message Authentication, Reporting, and Conformance): The Policy Layer

**DMARC, defined in RFC 7489, is the policy and reporting layer that sits on top of SPF and DKIM. It solves a critical problem that SPF and DKIM alone cannot: it tells receiving mail servers what to _do_ when authentication checks fail, and it provides the domain owner with visibility into how their domain is being used across the internet.**

### The DMARC Alignment Concept: Why SPF and DKIM Alone Aren't Enough

Here's the subtle but crucial point that many implementations get wrong: SPF and DKIM can both pass, yet a phishing email can still slip through. This is because SPF validates the **envelope From** (invisible to users), while the user-visible **header From** can be completely different. DMARC introduces **identifier alignment** — it requires that the domain in the header From address aligns with the domain that passed SPF or DKIM.

DMARC defines two alignment modes:

- **Relaxed alignment (default):** The organizational domain of the header From must match the organizational domain of the authenticated identifier. For example, if header From is _user@mail.yourdomain.com_, SPF passing for _yourdomain.com_ satisfies relaxed alignment.
- **Strict alignment (aspf=s; adkim=s):** The domains must match exactly. _mail.yourdomain.com_ and _yourdomain.com_ would not align under strict mode.

### Anatomy of a Real DMARC Record

```python
; DMARC TXT record
; Published at: _dmarc.yourdomain.com IN TXT
; Stage 1: Monitor-only policy (start here)
"v=DMARC1; p=none; rua=mailto:dmarc-reports@yourdomain.com; ruf=mailto:dmarc-failures@yourdomain.com; fo=1; ri=86400; pct=100"
; Stage 2: Quarantine policy (after analyzing reports for 30-60 days)
"v=DMARC1; p=quarantine; pct=25; rua=mailto:dmarc-reports@yourdomain.com; ruf=mailto:dmarc-failures@yourdomain.com; fo=1; aspf=r; adkim=r"
; Stage 3: Full enforcement (production-ready, after confirming legitimate traffic passes)
"v=DMARC1; p=reject; pct=100; rua=mailto:dmarc-reports@yourdomain.com; ruf=mailto:dmarc-failures@yourdomain.com; fo=1; aspf=r; adkim=r; ri=86400"
; Tag breakdown:
; v=DMARC1          - Version (required)
; p=none|quarantine|reject - Policy for failing messages
; pct=100           - Percentage of messages to apply policy to (1-100)
; rua=mailto:...    - Aggregate report destination (daily XML reports)
; ruf=mailto:...    - Forensic/failure report destination (per-message, privacy-sensitive)
; fo=0|1|d|s        - Failure reporting options:
;                     0 = report if both SPF and DKIM fail (default)
;                     1 = report if either SPF or DKIM fails (recommended)
;                     d = report if DKIM fails
;                     s = report if SPF fails
; aspf=r|s          - SPF alignment mode: r=relaxed (default), s=strict
; adkim=r|s         - DKIM alignment mode: r=relaxed (default), s=strict
; ri=86400          - Reporting interval in seconds (86400 = 24 hours, default)
; sp=               - Subdomain policy (inherits p= if not specified)
```

### The DMARC Rollout Strategy: None → Quarantine → Reject

Jumping directly to **p=reject** is one of the most common and damaging mistakes organizations make. A phased rollout is essential to avoid disrupting legitimate email flows you may not even be aware of — newsletter services, third-party CRMs, HR platforms, and partner integrations all send email on your behalf.

- **Phase 1 — Monitor (p=none, 30–60 days):** Publish your DMARC record with p=none and configure aggregate reporting (rua). Analyze the XML reports to identify all sources sending email as your domain. No messages are rejected during this phase.
- **Phase 2 — Quarantine with low percentage (p=quarantine; pct=10–25):** Once you've identified and authorized all legitimate senders, move to quarantine with a low pct value. This applies the policy to a small percentage of failing messages, allowing you to catch edge cases.
- **Phase 3 — Quarantine at scale (p=quarantine; pct=100):** Increase pct to 100 over 2–4 weeks, monitoring reports for unexpected failures.
- **Phase 4 — Reject (p=reject; pct=100):** Full enforcement. Unauthorized senders receive a permanent 550 rejection. This is the target state for all domains.

---

## Step-by-Step Implementation Guide: Configuring SPF, DKIM, and DMARC

Theory is valuable, but implementation is where deliverability is won or lost. This section provides a complete, production-ready configuration workflow for a domain that sends email through Google Workspace, SendGrid for transactional email, and a self-hosted Postfix server for system notifications.

### Step 1: Audit Your Current Sending Sources

Before touching any DNS records, you must know every service that sends email as your domain. Use the following Python script to query your existing DNS records and identify your current authorization state:

```python
import dns.resolver
import json
import sys
from typing import Optional
def audit_email_dns_records(domain: str) -> dict:
"""
Audit SPF, DKIM (common selectors), and DMARC records for a domain.
Requires: pip install dnspython
"""
results = {
"domain": domain,
"spf": None,
"dmarc": None,
"dkim_selectors": {},
"errors": []
}
# Common DKIM selectors used by major ESPs
common_selectors = [
"google", "selector1", "selector2", "s1", "s2",
"mail", "dkim", "k1", "k2", "sendgrid", "mailchimp",
"smtp", "default", "email"
]
# Query SPF record
try:
answers = dns.resolver.resolve(domain, 'TXT')
for rdata in answers:
txt_string = b''.join(rdata.strings).decode('utf-8')
if txt_string.startswith('v=spf1'):
results["spf"] = {
"record": txt_string,
"has_hard_fail": txt_string.endswith('-all'),
"has_soft_fail": '~all' in txt_string,
"mechanism_count": sum(1 for m in ['include:', 'a:', 'mx:', 'ptr:', 'exists:']
if m in txt_string)
}
except dns.resolver.NXDOMAIN:
results["errors"].append(f"Domain {domain} does not exist in DNS")
except dns.resolver.NoAnswer:
results["errors"].append("No TXT records found (no SPF record)")
except Exception as e:
results["errors"].append(f"SPF lookup error: {str(e)}")
# Query DMARC record
dmarc_domain = f"_dmarc.{domain}"
try:
answers = dns.resolver.resolve(dmarc_domain, 'TXT')
for rdata in answers:
txt_string = b''.join(rdata.strings).decode('utf-8')
if txt_string.startswith('v=DMARC1'):
# Parse DMARC tags
tags = {}
for tag in txt_string.split(';'):
tag = tag.strip()
if '=' in tag:
key, value = tag.split('=', 1)
tags[key.strip()] = value.strip()
results["dmarc"] = {
"record": txt_string,
"policy": tags.get('p', 'not set'),
"subdomain_policy": tags.get('sp', 'inherits p'),
"pct": tags.get('pct', '100'),
"rua": tags.get('rua', 'not configured'),
"ruf": tags.get('ruf', 'not configured'),
"alignment_spf": tags.get('aspf', 'relaxed'),
"alignment_dkim": tags.get('adkim', 'relaxed')
}
except dns.resolver.NXDOMAIN:
results["errors"].append("No DMARC record fou...
```

### Step 2: Integrate Email Verification with MailValid API

Before sending to any address, verifying that the recipient email is valid, the domain has proper MX records, and the mailbox exists is critical to maintaining a clean sender reputation. High bounce rates — anything above 2% — trigger reputation penalties at major inbox providers. Here's a production-ready integration with the MailValid API that includes proper error handling, retry logic, and result interpretation:

```python
import requests
import time
import logging
from typing import Optional
from dataclasses import dataclass
# Configure logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)
@dataclass
class EmailVerificationResult:
email: str
is_valid: bool
is_deliverable: bool
is_disposable: bool
is_role_account: bool
mx_records_found: bool
smtp_check_passed: bool
spf_record_found: bool
dmarc_record_found: bool
risk_score: float
suggestion: Optional[str]
raw_response: dict
def verify_email_with_mailvalid(
email: str,
api_key: str = "mv_live_key",
max_retries: int = 3,
retry_delay: float = 1.0
) -> Optional[EmailVerificationResult]:
"""
Verify an email address using the MailValid API.
Includes retry logic for transient failures and comprehensive result parsing.
Args:
email: The email address to verify
api_key: Your MailValid API key (mv_live_key for production)
max_retries: Number of retry attempts for transient errors
retry_delay: Base delay between retries (exponential backoff applied)
Returns:
EmailVerificationResult dataclass or None if verification failed
"""
url = "https://mailvalid.io/api/v1/verify"
headers = {
"X-API-Key": api_key,
"Content-Type": "application/json",
"Accept": "application/json"
}
payload = {"email": email}
for attempt in range(max_retries):
try:
response = requests.post(
url,
headers=headers,
json=payload,
timeout=10  # 10-second timeout for SMTP validation
)
# Handle rate limiting (429) with backoff
if response.status_code == 429:
retry_after = int(response.headers.get('Retry-After', retry_delay * (2 ** attempt)))
logger.warning(f"Rate limited. Retrying after {retry_after}s (attempt {attempt + 1})")
time.sleep(retry_after)
continue
# Handle server errors (5xx) with exponential backoff
if response.status_code >= 500:
wait_time = retry_delay * (2 ** attempt)
logger.warning(f"Server error {response.status_code}. Retrying in {wait_time}s")
time.sleep(wait_time)
continue
# Handl...
```

---

## Monitoring and Reporting: Turning DMARC Data into Actionable Intelligence

Publishing DMARC records with **rua** (aggregate reports) and **ruf** (forensic reports) tags is only the beginning. The real value lies in systematically parsing and acting on the data these reports provide. According to Validity's research, organizations that actively monitor DMARC reports resolve authentication failures 3x faster than those that ignore them.

### Understanding DMARC Aggregate Reports (XML Format)

DMARC aggregate reports are delivered as gzip-compressed XML files, typically once per day. Each report covers a 24-hour period and contains information about every email claiming to be from your domain that was received by the reporting server. Here's an annotated example of a real DMARC aggregate report structure:

```python
<?xml version="1.0" encoding="UTF-8" ?>
<!-- DMARC Aggregate Report (RUA) - RFC 7489 Appendix C -->
<feedback>
<report_metadata>
<org_name>google.com</org_name>
<email>noreply-dmarc-support@google.com</email>
<report_id>12345678901234567890</report_id>
<date_range>
<begin>1704067200</begin>   <!-- Unix timestamp: 2024-01-01 00:00:00 UTC -->
<end>1704153600</end>       <!-- Unix timestamp: 2024-01-02 00:00:00 UTC -->
</date_range>
</report_metadata>
<policy_published>
<domain>yourdomain.com</domain>
<adkim>r</adkim>    <!-- DKIM alignment: relaxed -->
<aspf>r</aspf>      <!-- SPF alignment: relaxed -->
<p>reject
</p>       <!-- Domain policy -->
<sp>reject</sp>     <!-- Subdomain policy -->
<pct>100</pct>      <!-- Policy percentage -->
</policy_published>
<!-- Record 1: Legitimate email from Google Workspace - PASS -->
<record>
<row>
<source_ip>209.85.220.41</source_ip>   <!-- Google mail server -->
<count>1547</count>                     <!-- 1,547 messages from this IP today -->
<policy_evaluated>
<disposition>none</disposition>        <!-- No action taken (passed) -->
<dkim>pass</dkim>
<spf>pass</spf>
</policy_evaluated>
</row>
<identifiers>
<header_from>yourdomain.com</header_from>
</identifiers>
<auth_results>
<dkim>
<domain>yourdomain.com</domain>
<selector>google</selector>
<result>pass</result>
</dkim>
<spf>
<domain>yourdomain.com</domain>
<result>pass</result>
</spf>
</auth_results>
</record>
<!-- Record 2: Suspicious source - FAIL (potential spoofing attempt) -->
<record>
<row>
<source_ip>192.0.2.100</source_ip>    <!-- Unknown/unauthorized IP -->
<count>23</count>                      <!-- 23 spoofing attempts today -->
<policy_evaluated>
<disposition>reject</disposition>    <!-- Rejected per p=reject policy -->
<dkim>fail</dkim>
<spf>fail</spf>
</policy_evaluated>
</row>
<identifiers>
<header_from>yourdomain.com</header_from>
</identifiers>
<auth_results>
<dkim>
<domain>yourdomain.com</domain>
<result>fail</result>
</dkim>
<spf>
<domain>yourdomain.com...
```

### Automated DMARC Report Processing

Manually parsing XML reports is impractical at scale. You have several options: commercial DMARC reporting platforms (dmarcian, Valimail, PowerDMARC), open-source parsers, or building your own pipeline. For teams that want full control, here is a Python script that parses DMARC aggregate reports and identifies issues requiring attention:

---

---

## Start Verifying Emails with MailValid

Everything in this guide is built into MailValid's **email verification API**:

- **95%+ accuracy** with syntax, MX, and SMTP validation
- **<200ms response time** for real-time checks
- **$0.001 per email** — 6x cheaper than ZeroBounce, 8x cheaper than NeverBounce
- **Credits never expire** — pay once, use whenever
- **100 free credits** to start — no credit card required

```python
import requests

response = requests.post(
    "https://mailvalid.io/api/v1/verify",
    headers={"X-API-Key": "mv_live_your_key", "Content-Type": "application/json"},
    json={"email": "user@example.com"}
)
print(response.json())
```

**[→ Start free with 100 credits at mailvalid.io](https://mailvalid.io/register)**

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