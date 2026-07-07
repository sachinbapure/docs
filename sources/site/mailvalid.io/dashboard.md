# Source: https://mailvalid.io/dashboard

Payment successful! Your credits have been added.

Payment didn't go through — no credits were added. Please try again or use a different card.

Your subscription is paused

Your last renewal payment didn't go through. Update your card to keep your plan and monthly credits.

Update payment method

# Dashboard

[Log out](https://mailvalid.io/login)

Credit Balance

—

Buy credits

Total Verifications

—

API Keys

—

## Get started

Finish setup to start cleaning your lists.

0/4

Create your API key Create

Verify your first email — use the box below

Run a bulk verification [See how](https://mailvalid.io/docs#bulk-verification)

Integrate the API [Read docs](https://mailvalid.io/docs)

## Plan & limits

Your current tier sets your rate limits and bulk size.

—

Requests / min

—

Reads / min

—

Max bulk size

—

[Upgrade plan](https://mailvalid.io/pricing) Manage billing Downgrade plan

## API Key

Use this key in the `X-API-Key` header

New Key

## Test Verification

Try a single email address

 Verify up to 1 credit

Result

## Verify a list

Upload a CSV or TXT file — we'll find the email addresses automatically.

Sample CSV

Drag & drop your file here, or browse

CSV or TXT · up to 10,000 emails · we auto-detect the email column

file.csv

—

Choose a different file Verify list

## Recent bulk jobs

Live status of your last batches. We also email you when each one finishes.

Loading…

## Understanding Responses

### Key Fields

`is_valid` Primary indicator — `true` means deliverable

`status` Detailed verification result

`is_catch_all` Domain accepts all — use with caution

`is_disposable` Temporary email — low quality

### Status Values

valid Safe to send

invalid Will bounce

catch\_all Accepts everything

unknown Could not verify (free)

do\_not\_mail Risky (disposable/role)

**Recommendation:** Only send to addresses where `is_valid: true` AND `is_disposable: false`.

API Usage Copy

curl -X POST /api/v1/verify/single \\
 -H "X-API-Key: YOUR\_API\_KEY" \\
 -H "Content-Type: application/json" \\
 -d '{"email": "test@gmail.com"}'

Support

## Raise a support ticket

We'll reply to your account email.

API not working Billing / Credits Accuracy issue Integration help Feature request Other

Cancel Submit ticket

## Downgrade your plan

If you're on a recurring plan, the change takes effect at the **end of your current billing period** — you keep your current limits until then, with no extra charge or refund. **Your existing credits are always kept** and never expire.

New plan

Cancel Confirm downgrade

## Confirm subscription

Monthly recurring billing

Cancel anytime from your dashboard (Downgrade plan → Free). Cancelling stops future charges — you're not billed again after the current month.

Cancel Continue to checkout

## Create API key

Give this key a name so you can recognize it later.

Key name 

Cancel Create key

## Your new API key

Copy this now — for security it won't be shown again.

Copy

Done

## Buy credits

Credits never expire. Pick a pack to keep verifying.

Loading plans…