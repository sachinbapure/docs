# Source: https://mailvalid.io/docs

# Email Verification API Documentation

Complete reference for the Mailvalid Email Verification API. Verify email addresses in real-time with syntax, domain, MX, and SMTP checks.

Integrating with an AI agent? Copy docs for AI [view raw](https://mailvalid.io/llms-full.txt) Copies a single self-contained guide — paste it into any AI agent.

🚀

### Fast & Reliable

Average response time under 500ms

🎯

### 95%+ Accuracy

Provider-optimized verification

📦

### Bulk Support

Up to 10,000 emails per request

Base URL

```
https://mailvalid.io/api/v1
```

## Authentication

All API requests require authentication using an API key. Include your API key in the `X-API-Key` header.

Get your API key

Create a free account at [mailvalid.io/register](https://mailvalid.io/register) and generate an API key from your [dashboard](https://mailvalid.io/dashboard/api-keys).

### Step-by-Step Setup

1. 1
 
 Create an account
 
 Sign up at [mailvalid.io/register](https://mailvalid.io/register) — 100 free credits included.
 
2. 2
 
 Generate an API key
 
 Go to [Dashboard → API Keys](https://mailvalid.io/dashboard/api-keys) and click "Create New Key".
 
3. 3
 
 Add the header to your requests
 
 Include `X-API-Key: mv_live_your_key_here` in all API requests.
 

Example Request Copy

```
curl -X POST https://mailvalid.io/api/v1/verify/single \
  -H "X-API-Key: mv_live_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com"}'
```

## Quickstart

Get started verifying emails in under 2 minutes. Choose your preferred language:

cURL Python JavaScript PHP

cURL Copy

```
# Verify a single email
curl -X POST https://mailvalid.io/api/v1/verify/single \
  -H "X-API-Key: mv_live_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{"email": "john@company.com"}'
```

Python Copy

```
import requests

API_KEY = "mv_live_your_key_here"

response = requests.post(
    "https://mailvalid.io/api/v1/verify/single",
    headers={"X-API-Key": API_KEY},
    json={"email": "john@company.com"}
)

result = response.json()
print(f"Status: {result['result']['status']}")  # valid, invalid, catch_all, unknown, do_not_mail
print(f"Valid: {result['result']['is_valid']}")
print(f"Confidence: {result['result']['confidence_score']}")
print(f"Reason: {result['result']['status_reason']}")
```

JavaScript (Node.js) Copy

```
const API_KEY = "mv_live_your_key_here";

const response = await fetch(
  "https://mailvalid.io/api/v1/verify/single",
  {
    method: "POST",
    headers: {
      "X-API-Key": API_KEY,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ email: "john@company.com" })
  }
);

const data = await response.json();
console.log(`Status: ${data.result.status}`);  // valid, invalid, catch_all, unknown, do_not_mail
console.log(`Valid: ${data.result.is_valid}`);
console.log(`Confidence: ${data.result.confidence_score}`);
```

PHP Copy

```
<?php
$apiKey = "mv_live_your_key_here";

$ch = curl_init("https://mailvalid.io/api/v1/verify/single");
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_HTTPHEADER => [
        "X-API-Key: " . $apiKey,
        "Content-Type: application/json"
    ],
    CURLOPT_POSTFIELDS => json_encode(["email" => "john@company.com"])
]);

$result = json_decode(curl_exec($ch), true);
echo "Status: " . $result['result']['status'];  // valid, invalid, catch_all, unknown, do_not_mail
echo "Valid: " . ($result['result']['is_valid'] ? 'Yes' : 'No');
echo "Confidence: " . $result['result']['confidence_score'];
?>
```

## Single Email Verification

Verify a single email address in real-time. Returns comprehensive verification results including syntax, domain, MX records, and SMTP checks.

POST `/api/v1/verify/single`

#### Request Body

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `email` | string | Required | The email address to verify |

#### Example Request

cURL Copy

```
curl -X POST https://mailvalid.io/api/v1/verify/single \
  -H "X-API-Key: mv_live_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{"email": "john@company.com"}'
```

#### Example Response

JSON

```
{
  "success": true,
  "credits_used": 1,
  "result": {
    "email": "john@company.com",
    "status": "valid",
    "is_valid": true,
    "syntax_valid": true,
    "domain": "company.com",
    "domain_valid": true,
    "has_mx": true,
    "mx_records": [
      {"priority": 10, "host": "mx1.company.com"}
    ],
    "smtp_checked": true,
    "smtp_response_code": 250,
    "is_disposable": false,
    "is_role_based": false,
    "is_catch_all": false,
    "is_free_provider": false,
    "confidence_score": 95,
    "status_reason": "mailbox_confirmed",
    "provider": "corporate",
    "verification_time_ms": 234,
    "cached": false
  }
}
```

#### Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `status` | string | Verification status: `valid`, `invalid`, `catch_all`, `unknown`, `do_not_mail` |
| `is_valid` | boolean | Whether the email is safe to send to |
| `confidence_score` | integer | Confidence in the result (0–100) |
| `status_reason` | string | Machine-readable sub-status reason (see [Status Values](https://mailvalid.io/docs#status-values)) |
| `provider` | string | Detected email provider (google, microsoft, yahoo, apple, corporate, etc.) |
| `is_disposable` | boolean | Whether the domain is a disposable/temporary email provider |
| `is_role_based` | boolean | Whether the address is role-based (info@, support@, admin@) |
| `is_catch_all` | boolean | Whether the domain accepts all email addresses |
| `is_free_provider` | boolean | Whether the domain is a free email provider (gmail.com, yahoo.com, etc.) |
| `cached` | boolean | Whether MailValid returned a recent server-side result. `cached: true` uses 0 credits. |

Fair billing

`credits_used` is the final charge. Fresh **valid**, **invalid**, **catch\_all**, and **do\_not\_mail** results use 1 credit. **unknown** results and `cached: true` recent repeats use 0 credits.

## Bulk Email Verification

Verify up to 10,000 emails in a single request. Jobs are processed asynchronously. You can poll for status or receive a webhook notification when complete.

POST `/api/v1/verify/bulk`

#### Request Body

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `emails` | array | Required | Array of email addresses (max 10,000) |
| `webhook_url` | string | Optional | URL to receive completion webhook |

#### Submit Bulk Job

cURL Copy

```
curl -X POST https://mailvalid.io/api/v1/verify/bulk \
  -H "X-API-Key: mv_live_your_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "emails": [
      "john@company.com",
      "jane@example.org",
      "info@business.net"
    ],
    "webhook_url": "https://yourapp.com/webhooks/mailvalid"
  }'
```

#### Response (Job Created)

JSON

```
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "pending",
  "total_emails": 3,
  "credits_reserved": 3,
  "webhook_url": "https://yourapp.com/webhooks/mailvalid"
}
```

`credits_reserved` is the maximum possible charge and is reserved at submit time. Unused credits are released after processing. The final charge is returned as `credits_used` when the job completes.

#### Check Job Status

cURL Copy

```
curl https://mailvalid.io/api/v1/verify/bulk/550e8400-e29b-41d4-a716-446655440000 \
  -H "X-API-Key: mv_live_your_key_here"
```

#### Completed Job Response

JSON

```
{
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status": "completed",
  "total_emails": 3,
  "processed_emails": 3,
  "valid_count": 2,
  "invalid_count": 1,
  "credits_used": 3,
  "results": [
    {
      "email": "john@company.com",
      "status": "valid",
      "is_valid": true,
      // ... full result for each email
    }
  ]
}
```

## Webhooks

Get notified when your bulk verification jobs complete instead of polling. Provide a `webhook_url` when submitting a bulk job.

Verify webhook signatures

Every webhook includes an `X-Mailvalid-Signature` header: an HMAC-SHA256 of the **raw request body**, signed with your account's webhook secret. Find your secret in your dashboard (it's also returned as `webhook_secret` from `GET /api/auth/stats`). Recompute the HMAC over the exact bytes you receive and compare — do not re-serialize the JSON first, or it won't match.

#### Webhook Payload

JSON

```
{
  "event": "job.completed",
  "timestamp": "2026-01-30T10:30:00Z",
  "data": {
    "job_id": "550e8400-e29b-41d4-a716-446655440000",
    "status": "completed",
    "total_emails": 100,
    "processed_emails": 100,
    "valid_count": 85,
    "invalid_count": 15,
    "credits_used": 100,
    "started_at": "2026-01-30T10:28:00Z",
    "completed_at": "2026-01-30T10:30:00Z",
    "error_message": null,
    "results_summary": {
      "total": 100,
      "results_url": "https://mailvalid.io/api/v1/verify/bulk/550e8400-e29b-41d4-a716-446655440000"
    }
  }
}
```

#### Webhook Headers

| Header | Description |
| --- | --- |
| `X-Mailvalid-Signature` | HMAC-SHA256 signature of the payload |
| `X-Mailvalid-Event` | Event type: `job.completed`, `job.failed`, or `job.cancelled` |
| `Content-Type` | application/json |

#### Verifying the signature

Compute an HMAC-SHA256 of the **raw request body** using your `webhook_secret` and compare it to the `X-Mailvalid-Signature` header. Sign the bytes exactly as received — don't parse and re-serialize the JSON.

Python (Flask)

```
import hmac, hashlib
from flask import request, abort

WEBHOOK_SECRET = "your_webhook_secret"  # from GET /api/auth/stats

def handle_webhook():
    raw = request.get_data()  # raw bytes — do NOT use request.json
    expected = hmac.new(WEBHOOK_SECRET.encode(), raw, hashlib.sha256).hexdigest()
    sig = request.headers.get("X-Mailvalid-Signature", "")
    if not hmac.compare_digest(expected, sig):
        abort(401)  # signature mismatch — reject
    event = request.json
    # ... trust event and process ...
    return "", 200
```

## Bounce Webhook

Feed delivery and bounce data back to Mailvalid to improve verification accuracy over time. This is especially valuable for providers that block SMTP verification (Microsoft, Apple) where we can't confirm mailboxes directly.

Improve accuracy automatically

The more bounce data you send, the more accurate future verifications become. Historical data is checked before SMTP verification, so known addresses return results instantly.

POST `/api/v1/webhooks/bounce` API Key Required

#### Submit Bounce/Delivery Events

Send up to 1,000 bounce or delivery events per request. Hard bounces automatically invalidate cached verification results.

#### Event Types

| Event Type | Effect | Description |
| --- | --- | --- |
| `delivered` | Marks valid | Email was successfully delivered to inbox |
| `hard_bounce` | Marks invalid | Permanent bounce — mailbox doesn't exist (550) |
| `soft_bounce` | Recorded | Temporary bounce — mailbox full, server down (452) |
| `complaint` | Marks do\_not\_mail | Recipient marked email as spam |
| `unsubscribe` | Recorded | Recipient unsubscribed from emails |

#### Request Body

JSON

```
{
  "events": [
    {
      "email": "user@example.com",
      "event_type": "hard_bounce",
      "bounce_code": "550",
      "bounce_message": "User unknown",
      "timestamp": "2026-03-14T10:30:00Z"
    },
    {
      "email": "valid@example.com",
      "event_type": "delivered"
    }
  ]
}
```

#### Example

cURL

```
curl -X POST https://mailvalid.io/api/v1/webhooks/bounce \
  -H "X-API-Key: mv_live_your_key" \
  -H "Content-Type: application/json" \
  -d '{
    "events": [
      {"email": "bounced@example.com", "event_type": "hard_bounce", "bounce_code": "550"},
      {"email": "good@example.com", "event_type": "delivered"}
    ]
  }'
```

#### Response

JSON

```
{
  "success": true,
  "processed": 2,
  "errors": 0,
  "message": "Processed 2/2 events"
}
```

## Job Management

List, download results from, and cancel your bulk verification jobs.

GET `/api/v1/verify/bulk`

#### List Your Jobs

Get a paginated list of all your bulk verification jobs.

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `page` | int | 1 | Page number |
| `page_size` | int | 20 | Items per page (max 100) |
| `status` | string | all | Filter by status |

cURL

```
curl "https://mailvalid.io/api/v1/verify/bulk?page=1&status=completed" \
  -H "X-API-Key: mv_live_your_key_here"
```

GET `/api/v1/verify/bulk/{job_id}/download`

#### Download Results

Download verification results as JSON or CSV.

cURL

```
# Download as CSV
curl "https://mailvalid.io/api/v1/verify/bulk/{job_id}/download?format=csv" \
  -H "X-API-Key: mv_live_your_key_here" \
  -o results.csv

# Download as JSON
curl "https://mailvalid.io/api/v1/verify/bulk/{job_id}/download?format=json" \
  -H "X-API-Key: mv_live_your_key_here" \
  -o results.json
```

DELETE `/api/v1/verify/bulk/{job_id}`

#### Cancel a Job

Cancel a pending or processing job. Completed billable rows use credits; unused reserved credits are released.

cURL

```
curl -X DELETE https://mailvalid.io/api/v1/verify/bulk/{job_id} \
  -H "X-API-Key: mv_live_your_key_here"
```

## MCP Server (Model Context Protocol)

Connect AI agents and assistants — Claude, Cursor, and any MCP-compatible client — directly to MailValid. The MCP server is a hosted remote endpoint over the **Streamable HTTP** transport. Its tools run through the same verification engine and credit system as the REST API, so billing is identical.

| **Endpoint** | `https://mailvalid.io/mcp/` |
| --- | --- |
| **Transport** | Streamable HTTP (use the `http` transport in your client) |
| **Authentication** | Your API key in the `X-API-Key` header (or `Authorization: Bearer mv_live_...`) |
| **Billing** | Identical to the REST API — single verification costs 1 credit, bulk reserves credits, reads are free |

Two tools — `verify_email_demo` and `get_api_discovery` — work without a key. Every other tool requires the `X-API-Key` header and bills your account.

#### Option 1 — the `@mailvalid/mcp` launcher (easiest, works in any client)

Works everywhere, including stdio-only clients like Claude Desktop. Add this to your client's MCP config — it reads your key from `MAILVALID_API_KEY` and bridges to this server, so there's no URL or header to manage:

JSON

```
{
  "mcpServers": {
    "mailvalid": {
      "command": "npx",
      "args": ["-y", "@mailvalid/mcp"],
      "env": { "MAILVALID_API_KEY": "mv_live_your_key_here" }
    }
  }
}
```

#### Option 2 — connect directly over HTTP (Claude Code, Cursor, …)

Clients with native remote-MCP support can point straight at the endpoint. With Claude Code:

Terminal Copy

```
claude mcp add --transport http mailvalid https://mailvalid.io/mcp/ \
  --header "X-API-Key: mv_live_your_key_here"
```

Or with an `mcp.json` config:

JSON

```
{
  "mcpServers": {
    "mailvalid": {
      "url": "https://mailvalid.io/mcp/",
      "headers": { "X-API-Key": "mv_live_your_key_here" }
    }
  }
}
```

Field names vary slightly between clients (some use `servers` instead of `mcpServers`) — check your client's MCP docs.

#### Available tools

| Tool | Auth | Cost | Description |
| --- | --- | --- | --- |
| `verify_email` | API key | 1 credit\* | Verify one address — syntax, DNS/MX, SMTP mailbox, disposable/role/catch-all, confidence. Args: `email`. |
| `submit_bulk_verification` | API key | Reserves N | Queue a list for asynchronous verification. Args: `emails` (array), optional `webhook_url`. |
| `get_bulk_job` | API key | Free | Status, progress, and (when complete) results of a bulk job. Args: `job_id`. |
| `list_bulk_jobs` | API key | Free | List your recent bulk jobs (paginated). Args: `page`, `page_size`, `status`. |
| `cancel_bulk_job` | API key | Free | Cancel a pending/processing job and release reserved credits. Args: `job_id`. |
| `get_credit_balance` | API key | Free | Your current credit balance and lifetime usage. |
| `verify_email_demo` | None | Free | Demo validation — syntax, disposable, role, and free-provider checks only (no SMTP). Rate-limited per IP. Args: `email`. |
| `get_api_discovery` | None | Free | MailValid machine-readable discovery URLs (OpenAPI, docs, well-known metadata). |

\* `verify_email` is free for `unknown` results and recent cached repeats. Discovery documents live at `/.well-known/mcp.json` and `/.well-known/mcp/server-card.json`. Tip: include the trailing slash (`/mcp/`) to avoid an HTTP redirect.

## HTTP Response Codes

| Code | Status | Description |
| --- | --- | --- |
| 200 | OK | Request successful |
| 201 | Created | Resource created (bulk job submitted) |
| 400 | Bad Request | Invalid request format or parameters |
| 401 | Unauthorized | Missing or invalid API key |
| 402 | Payment Required | Insufficient credits |
| 404 | Not Found | Resource not found |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Server Error | Internal server error |

## Email Status Values

Industry-standard status codes (same as ZeroBounce, NeverBounce). The `status` field indicates the verification result. The `status_reason` field gives the specific machine-readable reason.

#### Status Codes

| Status | is\_valid | Description | Action |
| --- | --- | --- | --- |
| `valid` | true | SMTP confirmed mailbox exists. Safe to send. (<2% bounce rate) | Safe to send |
| `invalid` | false | Address will bounce — bad syntax, no domain, or mailbox not found | Do not send |
| `catch_all` | false | Domain accepts all addresses. Cannot confirm individual mailbox | Send with caution |
| `unknown` | false | Could not determine validity (provider blocked SMTP, timeout). Free — 0 credits charged | Retry later or use bounce webhook |
| `do_not_mail` | false | Address may work but is risky — disposable, role-based (info@, support@), or spam complaint | Do not send marketing |

#### Sub-Status Reasons

The `status_reason` field gives the specific reason behind the status:

| status\_reason | Status | Meaning |
| --- | --- | --- |
| `mailbox_confirmed` | `valid` | SMTP server confirmed the mailbox exists (250 response) |
| `historical_delivery_confirmed` | `valid` | Email was previously delivered successfully (from bounce webhook data) |
| `mailbox_not_found` | `invalid` | SMTP server rejected the recipient (550 response) |
| `failed_syntax_check` | `invalid` | Email address format is invalid |
| `no_dns_entries` | `invalid` | Domain does not exist (no DNS records) |
| `no_mx_records` | `invalid` | Domain exists but has no MX records configured |
| `historical_hard_bounce` | `invalid` | Email previously hard-bounced (from bounce webhook data) |
| `accept_all` | `catch_all` | Domain accepts all addresses — individual mailbox cannot be confirmed |
| `failed_smtp_connection` | `unknown` | Provider blocks SMTP verification (Microsoft, Apple) |
| `antispam_system` | `unknown` | Anti-spam system blocked our verification attempt |
| `greylisted` | `unknown` | Server uses greylisting — temporarily rejected |
| `mail_server_temporary_error` | `unknown` | Mail server returned a temporary error |
| `disposable` | `do_not_mail` | Disposable/temporary email domain (e.g. mailinator.com) |
| `role_based` | `do_not_mail` | Role-based address (info@, support@, admin@) |
| `historical_complaint` | `do_not_mail` | Recipient previously filed a spam complaint (from bounce webhook data) |

#### Confidence Score

Every result includes a `confidence_score` (0–100) indicating how confident we are in the result:

| Range | Meaning |
| --- | --- |
| **90–100** | Very high confidence — SMTP confirmed, historical hard bounce, or syntax/domain failure |
| **70–89** | High confidence — historical delivery data, trusted provider response |
| **40–69** | Medium confidence — catch-all domain, DNS-only verification |
| **0–39** | Low confidence — provider blocked verification, timeout |

## Rate Limits

API requests are rate limited to ensure fair usage and platform stability.

| Limit Type | Value | Window |
| --- | --- | --- |
| Per API Key | **100 requests** | Per minute |

When rate limited, the API returns `429 Too Many Requests`. Back off and retry after the time specified in the `Retry-After` header.

**Need higher limits?** Contact us at [sachin@getmailvalid.com](mailto:sachin@getmailvalid.com) for custom rate limits on higher volume plans.

#### Rate Limit Headers

All responses include rate limit information:

| Header | Description |
| --- | --- |
| `X-RateLimit-Limit` | Maximum requests allowed in the current window |
| `X-RateLimit-Remaining` | Remaining requests in the current window |
| `Retry-After` | Seconds until rate limit resets (when limited) |

## Error Responses

When an error occurs, the API returns a consistent error format:

Error Response Format

```
{
  "detail": "Error message describing what went wrong"
}
```

#### Common Errors

| Error | Code | Solution |
| --- | --- | --- |
| Invalid API key | 401 | Check your API key is correct and active |
| Insufficient credits | 402 | Purchase more credits from your dashboard |
| Rate limit exceeded | 429 | Wait for the Retry-After period |
| Invalid email format | 422 | Check the email address format |
| Bulk job too large | 400 | Maximum 10,000 emails per request |

### Need Help?

Can't find what you're looking for? Try our interactive API docs or get in touch.

[Interactive API Docs](https://mailvalid.io/api/docs) [Contact Support](mailto:sachin@getmailvalid.com)