# Source: https://mailvalid.io/api/docs

# Email Verifier API 0.1.0 

OAS 3.1

[/api/openapi.json](https://mailvalid.io/api/openapi.json)

```
Email Verification API - Verify email addresses at scale.

## Features
- Single email verification
- Bulk email verification (async)
- Credit-based usage
- Detailed verification results

## Authentication
API endpoints require an API key passed in the `X-API-Key` header.
```

Authorize

### [Verification](https://mailvalid.io/api/docs#/Verification)

POST

[/api/v1/verify/single](https://mailvalid.io/api/docs#/Verification/verify_single_email_api_v1_verify_single_post)

Verify Single Email Address

POST

[/api/v1/verify/bulk](https://mailvalid.io/api/docs#/Verification/verify_bulk_emails_api_v1_verify_bulk_post)

Verify Bulk Emails

GET

[/api/v1/verify/bulk](https://mailvalid.io/api/docs#/Verification/list_bulk_jobs_api_v1_verify_bulk_get)

List Bulk Jobs

GET

[/api/v1/verify/bulk/{job\_id}](https://mailvalid.io/api/docs#/Verification/get_bulk_job_status_api_v1_verify_bulk__job_id__get)

Get Bulk Job Status

DELETE

[/api/v1/verify/bulk/{job\_id}](https://mailvalid.io/api/docs#/Verification/cancel_bulk_job_api_v1_verify_bulk__job_id__delete)

Cancel Bulk Job

GET

[/api/v1/verify/bulk/{job\_id}/download](https://mailvalid.io/api/docs#/Verification/download_bulk_results_api_v1_verify_bulk__job_id__download_get)

Download Bulk Results

### [Credits](https://mailvalid.io/api/docs#/Credits)

GET

[/api/v1/credits](https://mailvalid.io/api/docs#/Credits/get_credits_api_v1_credits_get)

Get Credits

### [System Tests](https://mailvalid.io/api/docs#/System%20Tests)

POST

[/api/v1/test/worker](https://mailvalid.io/api/docs#/System%20Tests/test_worker_api_v1_test_worker_post)

Test Worker

GET

[/api/v1/test/worker/status](https://mailvalid.io/api/docs#/System%20Tests/worker_status_api_v1_test_worker_status_get)

Worker Status

GET

[/api/v1/test/accuracy](https://mailvalid.io/api/docs#/System%20Tests/accuracy_stats_api_v1_test_accuracy_get)

Accuracy Stats

### [Webhooks](https://mailvalid.io/api/docs#/Webhooks)

POST

[/api/v1/webhooks/bounce](https://mailvalid.io/api/docs#/Webhooks/receive_bounce_webhook_api_v1_webhooks_bounce_post)

Receive Bounce Webhook

### [Payments](https://mailvalid.io/api/docs#/Payments)

GET

[/api/v1/payments/plans](https://mailvalid.io/api/docs#/Payments/list_plans_api_v1_payments_plans_get)

List Plans

POST

[/api/v1/payments/checkout](https://mailvalid.io/api/docs#/Payments/create_checkout_api_v1_payments_checkout_post)

Create Checkout

GET

[/api/v1/payments/plan](https://mailvalid.io/api/docs#/Payments/get_current_plan_api_v1_payments_plan_get)

Get Current Plan

POST

[/api/v1/payments/downgrade](https://mailvalid.io/api/docs#/Payments/downgrade_plan_api_v1_payments_downgrade_post)

Downgrade Plan

POST

[/api/v1/payments/portal](https://mailvalid.io/api/docs#/Payments/create_billing_portal_api_v1_payments_portal_post)

Create Billing Portal

POST

[/api/v1/payments/webhook](https://mailvalid.io/api/docs#/Payments/handle_webhook_api_v1_payments_webhook_post)

Handle Webhook

### [Authentication](https://mailvalid.io/api/docs#/Authentication)

POST

[/api/auth/register](https://mailvalid.io/api/docs#/Authentication/register_api_auth_register_post)

Register

POST

[/api/auth/agent/register](https://mailvalid.io/api/docs#/Authentication/agent_register_api_auth_agent_register_post)

Agent Register

POST

[/api/auth/agent/claim](https://mailvalid.io/api/docs#/Authentication/agent_claim_api_auth_agent_claim_post)

Agent Claim

POST

[/api/auth/login](https://mailvalid.io/api/docs#/Authentication/login_api_auth_login_post)

Login

POST

[/api/auth/forgot-password](https://mailvalid.io/api/docs#/Authentication/forgot_password_api_auth_forgot_password_post)

Forgot Password

POST

[/api/auth/reset-password](https://mailvalid.io/api/docs#/Authentication/reset_password_api_auth_reset_password_post)

Reset Password

GET

[/api/auth/me](https://mailvalid.io/api/docs#/Authentication/get_current_user_info_api_auth_me_get)

Get Current User Info

GET

[/api/auth/api-keys](https://mailvalid.io/api/docs#/Authentication/list_api_keys_api_auth_api_keys_get)

List Api Keys

POST

[/api/auth/api-keys](https://mailvalid.io/api/docs#/Authentication/create_api_key_api_auth_api_keys_post)

Create Api Key

DELETE

[/api/auth/api-keys/{key\_id}](https://mailvalid.io/api/docs#/Authentication/revoke_api_key_api_auth_api_keys__key_id__delete)

Revoke Api Key

GET

[/api/auth/credits](https://mailvalid.io/api/docs#/Authentication/get_user_credits_api_auth_credits_get)

Get User Credits

GET

[/api/auth/stats](https://mailvalid.io/api/docs#/Authentication/get_user_stats_api_auth_stats_get)

Get User Stats

### [support](https://mailvalid.io/api/docs#/support)

POST

[/api/support](https://mailvalid.io/api/docs#/support/submit_support_api_support_post)

Submit Support

GET

[/api/support/tickets](https://mailvalid.io/api/docs#/support/list_tickets_api_support_tickets_get)

List Tickets

### [Newsletter](https://mailvalid.io/api/docs#/Newsletter)

POST

[/api/newsletter/subscribe](https://mailvalid.io/api/docs#/Newsletter/newsletter_subscribe_api_newsletter_subscribe_post)

Newsletter Subscribe

### [default](https://mailvalid.io/api/docs#/default)

POST

[/api/demo/verify](https://mailvalid.io/api/docs#/default/demo_verify_email_api_demo_verify_post)

Demo Verify Email

GET

[/](https://mailvalid.io/api/docs#/default/landing_page__get)

Landing Page

GET

[/pricing](https://mailvalid.io/api/docs#/default/pricing_page_pricing_get)

Pricing Page

GET

[/login](https://mailvalid.io/api/docs#/default/login_page_login_get)

Login Page

GET

[/register](https://mailvalid.io/api/docs#/default/register_page_register_get)

Register Page

GET

[/signup](https://mailvalid.io/api/docs#/default/signup_redirect_signup_get)

Signup Redirect

GET

[/status](https://mailvalid.io/api/docs#/default/status_page_status_get)

Status Page

GET

[/forgot-password](https://mailvalid.io/api/docs#/default/forgot_password_page_forgot_password_get)

Forgot Password Page

GET

[/reset-password](https://mailvalid.io/api/docs#/default/reset_password_page_reset_password_get)

Reset Password Page

GET

[/agent/claim](https://mailvalid.io/api/docs#/default/agent_claim_page_agent_claim_get)

Agent Claim Page

GET

[/dashboard](https://mailvalid.io/api/docs#/default/dashboard_page_dashboard_get)

Dashboard Page

GET

[/dashboard/api-keys](https://mailvalid.io/api/docs#/default/api_keys_page_dashboard_api_keys_get)

Api Keys Page

GET

[/alternatives/zerobounce](https://mailvalid.io/api/docs#/default/zerobounce_alternative_alternatives_zerobounce_get)

Zerobounce Alternative

GET

[/alternatives/neverbounce](https://mailvalid.io/api/docs#/default/neverbounce_alternative_alternatives_neverbounce_get)

Neverbounce Alternative

GET

[/alternatives/hunter](https://mailvalid.io/api/docs#/default/hunter_alternative_alternatives_hunter_get)

Hunter Alternative

GET

[/alternatives](https://mailvalid.io/api/docs#/default/alternatives_index_alternatives_get)

Alternatives Index

GET

[/blog](https://mailvalid.io/api/docs#/default/blog_page_blog_get)

Blog Page

GET

[/blog/{slug}](https://mailvalid.io/api/docs#/default/blog_post_page_blog__slug__get)

Blog Post Page

GET

[/faq](https://mailvalid.io/api/docs#/default/faq_page_faq_get)

Faq Page

GET

[/docs](https://mailvalid.io/api/docs#/default/api_docs_page_docs_get)

Api Docs Page

GET

[/support](https://mailvalid.io/api/docs#/default/support_page_support_get)

Support Page

GET

[/use-cases](https://mailvalid.io/api/docs#/default/use_cases_page_use_cases_get)

Use Cases Page

GET

[/terms](https://mailvalid.io/api/docs#/default/terms_page_terms_get)

Terms Page

GET

[/privacy](https://mailvalid.io/api/docs#/default/privacy_page_privacy_get)

Privacy Page

GET

[/refund](https://mailvalid.io/api/docs#/default/refund_page_refund_get)

Refund Page

GET

[/sitemap.xml](https://mailvalid.io/api/docs#/default/sitemap_sitemap_xml_get)

Sitemap

GET

[/robots.txt](https://mailvalid.io/api/docs#/default/robots_robots_txt_get)

Robots

GET

[/health](https://mailvalid.io/api/docs#/default/health_check_health_get)

Health Check

GET

[/health/rapidapi](https://mailvalid.io/api/docs#/default/rapidapi_health_health_rapidapi_get)

Rapidapi Health

#### Schemas

AgentClaimRequest

Expand all**object**

AgentClaimResponse

Expand all**object**

AgentRegisterRequest

Expand all**object**

AgentRegisterResponse

Expand all**object**

ApiKeyCreate

Expand all**object**

ApiKeyCreated

Expand all**object**

ApiKeyResponse

Expand all**object**

BounceEvent

Expand all**object**

BounceWebhookRequest

Expand all**object**

BounceWebhookResponse

Expand all**object**

BulkJobListResponse

Expand all**object**

BulkJobStatus

Expand all**object**

BulkJobSummary

Expand all**object**

BulkVerifyRequest

Expand all**object**

BulkVerifyResponse

Expand all**object**

CheckoutRequest

Expand all**object**

CreditBalance

Expand all**object**

DowngradeRequest

Expand all**object**

ForgotPasswordRequest

Expand all**object**

HTTPValidationError

Expand all**object**

LoginRequest

Expand all**object**

LoginResponse

Expand all**object**

MXRecord

Expand all**object**

RegisterRequest

Expand all**object**

ResetPasswordRequest

Expand all**object**

SingleVerifyRequest

Expand all**object**

SingleVerifyResponse

Expand all**object**

SupportRequest

Expand all**object**

UserBasic

Expand all**object**

ValidationError

Expand all**object**

VerificationResult

Expand all**object**

WorkerTestRequest

Expand all**object**

WorkerTestResponse

Expand all**object**