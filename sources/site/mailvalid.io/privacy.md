# Source: https://mailvalid.io/privacy

# Privacy Policy

Last updated: March 30, 2026

## 1\. Introduction

**Bits42 Internet Technologies Private Limited** ("we," "us," "our") operates the Mailvalid email verification platform ("Service"). This Privacy Policy explains how we collect, use, store, and protect your information when you use our Service.

By using the Service, you consent to the data practices described in this policy.

## 2\. Information We Collect

### Account Information

- Name and email address (when you register)
- Company name (optional)
- Hashed password (we never store plaintext passwords)
- API keys (stored as bcrypt hashes)

### Usage Data

- IP addresses and request timestamps
- API usage logs (endpoint, response time, status codes)
- Credit balance and transaction history
- Verification result metadata (email hash, status, timestamp)

### Email Addresses for Verification

- Email addresses submitted via API are processed solely for verification
- We store only a SHA-256 hash of verified emails for caching (not the plaintext)
- Plaintext email addresses are not stored after verification response is returned

### Payment Information

- Payment processing is handled entirely by DodoPayments
- We do not store credit card numbers or payment details
- We receive and store payment confirmation and transaction IDs

## 3\. How We Use Your Information

- **Service delivery:** Process email verifications, manage credits, and provide API access
- **Account management:** Authenticate users, manage API keys, and maintain security
- **Billing:** Process payments, track credit usage, and send invoices
- **Improvement:** Analyze usage patterns to improve accuracy and performance
- **Communication:** Send service updates, security alerts, and support responses
- **Legal compliance:** Comply with applicable laws and enforce our terms

## 4\. Data Retention

| Data Type | Retention Period |
| --- | --- |
| Account data | Until account deletion |
| Verification email hashes | 30 days (for caching) |
| API access logs | 90 days |
| Credit transactions | Duration of account + 7 years (legal requirement) |
| Payment records | As required by tax/accounting laws |

## 5\. Data Sharing

We do NOT sell, rent, or trade your data. We share data only with:

- **DodoPayments:** For payment processing (they have their own privacy policy)
- **Cloud infrastructure:** AWS for database hosting (data encrypted at rest)
- **Legal authorities:** When required by law, court order, or to protect our rights

## 6\. Security Measures

- Passwords hashed with bcrypt (never stored in plaintext)
- API keys hashed before storage (original shown only once at creation)
- Email addresses hashed (SHA-256) for verification caching
- HTTPS/TLS encryption for all API communications
- Rate limiting and IP-based security middleware
- Database access restricted to application servers only

## 7\. Your Rights (GDPR & Applicable Law)

Depending on your jurisdiction, you may have the right to:

- **Access:** Request a copy of your personal data
- **Correction:** Request correction of inaccurate data
- **Deletion:** Request deletion of your account and data
- **Portability:** Request data in a machine-readable format
- **Objection:** Object to certain processing activities

To exercise these rights, contact us at privacy@mailvalid.io. We will respond within 30 days.

## 8\. Cookies

We use essential cookies for:

- Session authentication (httpOnly JWT cookies for dashboard)
- Rate limiting (Redis-based, no cookies involved)

We do not use tracking cookies or third-party analytics cookies.

## 9\. Children's Privacy

The Service is not intended for users under 18 years of age. We do not knowingly collect data from children.

## 10\. International Data Transfers

Data is stored on servers located in the United States (AWS). By using the Service, you consent to the transfer of your data to the US. We ensure appropriate safeguards are in place for international transfers.

## 11\. Changes to This Policy

We may update this Privacy Policy periodically. Material changes will be notified via email at least 30 days before taking effect. Continued use of the Service constitutes acceptance.

## 12\. Contact

For privacy-related inquiries:

**Bits42 Internet Technologies Private Limited** 
Data Protection Officer 
Email: privacy@mailvalid.io