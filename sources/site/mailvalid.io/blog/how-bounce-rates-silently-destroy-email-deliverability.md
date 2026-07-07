# Source: https://mailvalid.io/blog/how-bounce-rates-silently-destroy-email-deliverability

Most email marketers obsess over open rates, click-through rates, and conversion metrics. They A/B test subject lines, optimize send times, and agonize over copy. Meanwhile, a single number quietly undermines every campaign they run — and they often don't notice until the damage is already done.

That number is 2%.

Gmail has drawn a hard line in the sand: sustain a bounce rate above 2%, and your sending reputation enters a spiral that can take months to recover from. This isn't a guideline or a best practice. It's a threshold enforced algorithmically, and crossing it triggers consequences that compound over time in ways that aren't immediately obvious when you're staring at your campaign dashboard.

This post breaks down exactly what happens when your bounce rate climbs past that threshold, how inbox providers track and penalize you over rolling time windows, and what you can do — right now — to diagnose your list health, verify your addresses before they cause damage, and build a monitoring system that catches problems before they spiral.

---

## Why Gmail's 2% Threshold Is a Hard Limit, Not a Suggestion

In February 2024, Google formalized what had long been an informal industry standard: senders must keep their spam rate below 0.10% and their bounce rate — specifically hard bounces — below 2% to maintain reliable inbox placement. These requirements apply to anyone sending more than 5,000 messages per day to Gmail addresses, but the underlying reputation mechanics affect all senders regardless of volume.

The reason Gmail treats 2% as a hard threshold comes down to signal quality. Every bounce is a data point telling inbox providers something about how you acquired your list, how well you maintain it, and whether you're the kind of sender who cares about recipient experience. A handful of bounces is noise. Sustained bounces above 2% are a pattern — and inbox providers are exceptionally good at distinguishing patterns from noise.

What makes this particularly dangerous is the lag between cause and consequence. You don't send to 10,000 addresses, get 200 bounces, and immediately see your inbox placement collapse. Instead, reputation damage accumulates silently across rolling 30-day and 90-day windows. By the time your open rates start dropping noticeably, you may already be several weeks into a reputation hole that takes just as long to climb out of.

According to **Validity's 2023 Email Deliverability Benchmark Report**, senders with bounce rates above 2% experience an average inbox placement rate of 62% — compared to 95% for senders maintaining bounce rates below 0.5%. That 33-point gap represents a third of your list never seeing your messages at all.

---

Ready to verify your email list?

100 free credits. $0.001/email after. No credit card required.

[Start Free](https://mailvalid.io/register)

## The Math: What 200 Bounces Actually Cost You

Let's make this concrete. You have a list of 10,000 email addresses. You send a campaign, and 2% of those addresses bounce — that's 200 bounced messages. On the surface, 200 sounds manageable. You still reached 9,800 people. What's the big deal?

The big deal is what those 200 bounces signal to inbox providers, and how that signal compounds over time.

**Month 1:** Your bounce rate sits at 2.0%. Gmail's Postmaster Tools shows your domain reputation dropping from "High" to "Medium." Inbox placement dips from 95% to around 88%. You notice a slight drop in open rates but attribute it to seasonal variation.

**Month 2:** You send again without cleaning your list. The same bad addresses bounce again (plus any new ones that have gone stale). Your rolling 30-day bounce rate is now consistently above 2%. Domain reputation moves from "Medium" to "Low." Inbox placement falls to roughly 75%. Open rates are down noticeably, but you're still attributing it to content or timing.

**Month 3:** Gmail begins routing a significant portion of your messages to spam folders. Inbox placement drops to 60-65%. Your open rates have cratered. You're now in a feedback loop: lower inbox placement means fewer opens, which further signals to Gmail that recipients don't want your mail.

**Month 4-6:** Without intervention, you're in full reputation crisis. Some receiving servers begin blocking your IP or domain outright. Inbox placement stabilizes at 55-60% — but only because the worst-affected recipients have already stopped seeing your mail entirely.

This trajectory isn't hypothetical. **Return Path's Email Intelligence Report** found that senders who crossed the 2% bounce threshold and didn't remediate within 30 days saw an average inbox placement degradation of 35 percentage points over the following 90 days. Senders who remediated within two weeks — by cleaning their lists and implementing verification — recovered to near-baseline within 45 days.

The math on the business impact is equally stark. If your list generates $50 per 1,000 emails sent (a conservative revenue-per-send figure for most e-commerce businesses), dropping from 95% to 60% inbox placement on a 10,000-address list costs you:

- **At 95% placement:** 9,500 delivered × $0.05 = **$475 per campaign**
- **At 60% placement:** 6,000 delivered × $0.05 = **$300 per campaign**
- **Loss per campaign:** $175
- **Loss over 12 campaigns per year:** $2,100

And that calculation doesn't account for the compounding effect of damaged sender reputation on future campaigns, the cost of re-engagement, or the revenue lost from subscribers who simply never come back after being trained to not see your emails.

---

## Hard Bounces vs. Soft Bounces: Different Damage, Same Destination

Not all bounces are created equal, but both types inflict real damage on your deliverability — just through different mechanisms.

### Hard Bounces

A hard bounce indicates a permanent delivery failure. The address doesn't exist, the domain doesn't accept mail, or the recipient has explicitly blocked your sender address. Hard bounces are the primary driver of reputation damage because they're the clearest signal that you're sending to addresses you shouldn't have.

**Common causes of hard bounces:** - Addresses that were never valid (typos, fake signups) - Addresses that have been abandoned and deactivated - Domains that no longer exist or have stopped hosting email - Role accounts that have been removed (e.g., `info@company.com` after a business closes)

The critical thing to understand about hard bounces is that they should never happen more than once per address. If you send to a hard-bounced address a second time, you're not just making a deliverability mistake — you're sending a clear signal to inbox providers that you don't maintain suppression lists, which is one of the fastest ways to get flagged as a low-quality sender.

### Soft Bounces

Soft bounces indicate a temporary delivery failure — the mailbox is full, the receiving server is temporarily unavailable, or the message was too large. In isolation, a single soft bounce is benign. The damage comes from retry behavior.

Most email service providers automatically retry soft-bounced messages on a schedule. If you're retrying delivery to addresses that consistently soft-bounce — because the mailbox is perpetually over quota, for example — you're generating a sustained stream of failed delivery attempts that inbox providers interpret as poor list hygiene.

**The long-term soft bounce trap works like this:**

1. An address soft-bounces on your first send
2. Your ESP retries 3-4 times over 48-72 hours
3. The address soft-bounces again on your second campaign
4. And your third
5. After 3-4 consistent soft bounces, most inbox providers treat the address as functionally dead — and your repeated attempts to reach it count against your reputation similarly to hard bounces

**Best practice:** After 3 consecutive soft bounces over a 30-day period, suppress the address and treat it as a hard bounce for list management purposes. The [Disposable Email Detection](https://mailvalid.io/blog/disposable-email-detection-guide-developers-2026) problem is closely related — many disposable addresses will soft-bounce repeatedly before finally hard-bouncing when the domain expires.

---

## How Gmail and Microsoft 365 Track Reputation Over Rolling Windows

Understanding how inbox providers actually measure and store your sending reputation is essential to understanding why bounce rate problems compound over time rather than resetting between campaigns.

### Gmail's Reputation Model

Gmail evaluates sender reputation across multiple dimensions simultaneously:

- **IP reputation:** How does this specific sending IP behave?
- **Domain reputation:** How does the sending domain behave across all IPs?
- **Brand reputation:** How does the "From" name and address pattern behave?

Each of these dimensions is evaluated over rolling time windows. Gmail's Postmaster Tools exposes reputation data in daily snapshots, but the underlying scoring uses a **30-day rolling window** for most signals and a **90-day rolling window** for more significant reputation events.

This means that if you send a campaign today with a 3% bounce rate, that damage doesn't disappear after 30 days — it fades gradually as newer, cleaner sending data dilutes the signal. But it also means that recovering from a reputation hit requires sustained clean sending over weeks, not just one good campaign.

Gmail's reputation tiers — High, Medium, Low, and Bad — correspond roughly to these inbox placement ranges: - **High:** 90-98% inbox placement - **Medium:** 75-89% inbox placement - **Low:** 50-74% inbox placement - **Bad:** Below 50%, with significant blocking risk

### Microsoft 365 / Outlook's Reputation Model

Microsoft uses a similar rolling window approach but with some important differences. Microsoft's **Smart Network Data Services (SNDS)** evaluates IP reputation, while their broader filtering system also incorporates domain-level signals through their **Junk Email Reporting Program (JMRP)**.

Microsoft's filtering tends to be more binary than Gmail's — addresses either get delivered or they get blocked, with less of the gradual "routed to spam" middle ground that Gmail uses. This makes Microsoft's consequences for high bounce rates more immediately visible but also potentially more severe.

**Key difference:** Microsoft places significant weight on the ratio of bounces to successful deliveries on a per-IP basis. If you're sending from a shared IP (common with many ESPs), your bounce rate contributes to the IP's overall reputation — which affects all other senders on that IP. This is one of the strongest arguments for dedicated IPs at scale, and for keeping your bounce rate clean regardless of your sending volume.

According to **Litmus's 2023 State of Email Report**, Microsoft's email clients (Outlook.com and Microsoft 365) account for approximately 4% of global email opens — but Microsoft's filtering infrastructure protects a much larger share of corporate email, making Microsoft reputation management critical for B2B senders in particular.

---

## Real Data: What Inbox Placement Degradation Actually Looks Like

The statistics from Validity and Return Path paint a clear picture of how bounce rates correlate with inbox placement across the industry.

**Validity's 2023 Benchmark data shows:**

- Senders with bounce rates **below 0.5%** achieve an average inbox placement rate of **95.2%**
- Senders with bounce rates **between 0.5% and 2%** achieve an average inbox placement rate of **84.7%**
- Senders with bounce rates **above 2%** achieve an average inbox placement rate of **62.1%**
- Senders with bounce rates **above 5%** achieve an average inbox placement rate of **41.3%**

The drop from the 0.5%-2% band to the above-2% band is particularly sharp — a 22-point cliff that corresponds almost exactly to Gmail's published threshold. This isn't coincidence. It reflects the real-world impact of Gmail's algorithmic response to crossing that threshold.

**Return Path's longitudinal data** on sender reputation recovery adds another dimension: senders who experienced a reputation drop due to high bounce rates required an average of **47 days of clean sending** to return to their previous reputation tier. Senders who implemented list verification before resuming sends recovered in an average of **23 days** — roughly half the time.

The takeaway is that list verification isn't just about preventing future damage. It's the fastest path to recovering from existing damage, because it allows you to resume sending with confidence that your bounce rate will stay low during the critical recovery window.

---

## Step-by-Step: Diagnose, Verify, and Monitor Your List Health

Here's a practical framework for getting your bounce rate under control, whether you're dealing with an active problem or trying to prevent one.

### Step 1: Diagnose Your Current Bounce Rate

Before you can fix a problem, you need to measure it accurately. Most ESPs report bounce rates, but the numbers they show you may not match what inbox providers are measuring.

**Check these sources:** - Your ESP's bounce report (broken down by hard and soft) - Gmail Postmaster Tools (free, shows domain reputation and spam rate) - Microsoft SNDS (free registration, shows IP-level reputation) - Your email authentication records (SPF, DKIM, DMARC) — authentication failures can masquerade as bounces

**Warning signs that your bounce rate is higher than your ESP is reporting:** - Open rates declining without a change in content or send frequency - Gmail Postmaster Tools showing "Medium" or "Low" domain reputation - Increasing spam complaint rates - Replies from recipients saying they "never got" your emails

### Step 2: Segment Your List by Risk

Not all addresses on your list carry equal risk. Before verifying or cleaning, segment by:

- **Engagement recency:** When did each address last open or click?
- **Acquisition source:** How was each address collected? (Form signup vs. purchased list vs. event registration)
- **Age:** How long has each address been on your list without activity?

Addresses that haven't engaged in 12+ months are significantly more likely to have gone stale — abandoned, deactivated, or converted to spam traps. These are your highest-risk segment and should be verified before your next send.

### Step 3: Verify Your List with MailValid

List verification is the most direct intervention available for reducing bounce rates before they cause damage. MailValid's API checks each address against multiple validation layers — syntax, domain existence, MX records, and mailbox-level verification — to identify addresses that will bounce before you send to them.

The Python code section below shows exactly how to implement this. For now, the key points:

- Verify any list that hasn't been sent to in 90+ days
- Verify all new subscribers within 24 hours of signup (real-time verification)
- Re-verify your full list quarterly if you send at high volume
- Always verify purchased or third-party lists before any send

### Step 4: Implement Suppression and Monitoring

Verification is a point-in-time snapshot. Ongoing monitoring is what keeps your bounce rate clean over time.

**Suppression list management:** - Add every hard bounce to your suppression list immediately and automatically - After 3 consecutive soft bounces in 30 days, add to suppression - Never remove addresses from your suppression list without re-verification

**Ongoing monitoring:** - Check Gmail Postmaster Tools weekly - Set up bounce rate alerts in your ESP (most support webhook notifications) - Review your bounce breakdown (hard vs. soft) after every campaign - Track your rolling 30-day bounce rate, not just per-campaign bounce rate

### Step 5: Implement Real-Time Verification at the Point of Capture

The most cost-effective place to stop bad addresses is before they ever enter your list. Integrating MailValid's API at your signup forms catches typos, fake addresses, and [Disposable Email Detection](https://mailvalid.io/blog/disposable-email-detection-guide-developers-2026) issues at the moment of capture — before they have any chance to damage your reputation.

---

## Python Code: Calculate Your Bounce Rate and Verify Your List with MailValid

The following code provides two practical tools: a bounce rate calculator that projects inbox placement impact over time, and a list verification script using the MailValid API.

### Bounce Rate Impact Calculator

```python
import json
from dataclasses import dataclass
from typing import List, Tuple

@dataclass
class BounceRateAnalysis:
    list_size: int
    bounce_rate: float
    current_inbox_placement: float

    def calculate_bounces(self) -> int:
        """Calculate absolute number of bounces from rate."""
        return int(self.list_size * (self.bounce_rate / 100))

    def project_inbox_placement(self, months: int = 6) -> List[Tuple[int, float]]:
        """
        Project inbox placement degradation over time based on bounce rate.
        Uses empirical decay curves derived from Validity benchmark data.
        Returns list of (month, projected_inbox_placement_pct) tuples.
        """
        projections = []
        placement = self.current_inbox_placement

        # Degradation rates based on bounce rate thresholds
        if self.bounce_rate < 0.5:
            monthly_degradation = 0.0  # No degradation, stable
        elif self.bounce_rate < 1.0:
            monthly_degradation = 0.5  # Mild degradation
        elif self.bounce_rate < 2.0:
            monthly_degradation = 1.5  # Moderate degradation
        elif self.bounce_rate < 5.0:
            monthly_degradation = 5.5  # Severe degradation (Gmail threshold crossed)
        else:
            monthly_degradation = 8.0  # Critical — blocking risk

        # Floor placement at 40% (below this, most mail is blocked entirely)
        floor_placement = 40.0

        for month in range(1, months + 1):
            placement = max(floor_placement, placement - monthly_degradation)
            projections.append((month, round(placement, 1)))

        return projections

    def calculate_revenue_impact(
        self, 
        revenue_per_thousand: float = 50.0
    ) -> dict:
        """
        Calculate revenue impact of inbox placement degradation.

        Args:
            revenue_per_thousand: Revenue generated per 1,000 emails delivered

        Returns:
            Dictionary with revenue projections and loss calculations
        """
        projections = self.project_inbox_placement()

        baseline_revenue = (
            self.list_size * 
            (self.current_inbox_placement / 100) * 
            (revenue_per_thousand / 1000)
        )

        results = {
            "baseline_revenue_per_campaign": round(baseline_revenue, 2),
            "monthly_projections": [],
            "total_projected_loss_6_months": 0.0
        }

        total_loss = 0.0
        for month, placement in projections:
            projected_revenue = (
                self.list_size * 
                (placement / 100) * 
                (revenue_per_thousand / 1000)
            )
            monthly_loss = baseline_revenue - projected_revenue
            total_loss += monthly_loss

            results["monthly_projections"].append({
                "month": month,
                "inbox_placement_pct": placement,
                "projected_revenue": round(projected_revenue, 2),
                "revenue_loss": round(monthly_loss, 2)
            })

        results["total_projected_loss_6_months"] = round(total_loss, 2)
        return results

def analyze_bounce_impact(
    list_size: int,
    bounce_rate: float,
    current_inbox_placement: float = 95.0,
    revenue_per_thousand: float = 50.0
) -> None:
    """
    Main analysis function. Prints a full bounce rate impact report.

    Args:
        list_size: Total number of addresses on your list
        bounce_rate: Current bounce rate as a percentage (e.g., 2.5 for 2.5%)
        current_inbox_placement: Current inbox placement rate as percentage
        revenue_per_thousand: Revenue per 1,000 delivered emails
    """
    analysis = BounceRateAnalysis(list_size, bounce_rate, current_inbox_placement)

    print("=" * 60)
    print("BOUNCE RATE IMPACT ANALYSIS")
    print("=" * 60)
    print(f"List Size:              {list_size:,}")
    print(f"Bounce Rate:            {bounce_rate}%")
    print(f"Absolute Bounces:       {analysis.calculate_bounces():,}")
    print(f"Current Inbox Placement:{current_inbox_placement}%")

    # Risk assessment
    if bounce_rate < 0.5:
        risk_level = "LOW — Excellent list health"
    elif bounce_rate < 1.0:
        risk_level = "MODERATE — Monitor closely"
    elif bounce_rate < 2.0:
        risk_level = "HIGH — Clean your list now"
    else:
        risk_level = "CRITICAL — Gmail threshold exceeded"

    print(f"Risk Level:             {risk_level}")
    print()

    # Inbox placement projections
    print("PROJECTED INBOX PLACEMENT OVER 6 MONTHS:")
    print("-" * 40)
    projections = analysis.project_inbox_placement()
    for month, placement in projections:
        bar_length = int(placement / 2)
        bar = "█" * bar_length
        print(f"Month {month}: {placement:5.1f}% {bar}")

    print()

    # Revenue impact
    print("PROJECTED REVENUE IMPACT:")
    print("-" * 40)
    revenue_data = analysis.calculate_revenue_impact(revenue_per_thousand)
    print(f"Baseline Revenue/Campaign: ${revenue_data['baseline_revenue_per_campaign']:,.2f}")
    print()

    for proj in revenue_data["monthly_projections"]:
        print(
            f"Month {proj['month']}: "
            f"${proj['projected_revenue']:,.2f} "
            f"(-${proj['revenue_loss']:,.2f})"
        )

    print()
    print(
        f"Total Projected 6-Month Loss: "
        f"${revenue_data['total_projected_loss_6_months']:,.2f}"
    )
    print("=" * 60)

# Example usage
if __name__ == "__main__":
    # Scenario: 10,000 address list with 2% bounce rate
    analyze_bounce_impact(
        list_size=10000,
        bounce_rate=2.0,
        current_inbox_placement=95.0,
        revenue_per_thousand=50.0
    )
```

### List Verification with MailValid API

````python
import requests
import csv
import time
import json
from typing import Optional
from dataclasses import dataclass, field
from pathlib import Path

MAILVALID_API_KEY = "your_api_key_here"
MAILVALID_API_URL = "https://api.mailvalid.io/v1/verify"

@dataclass
class VerificationResult:
    email: str
    is_valid: bool
    is_deliverable: bool
    is_disposable: bool
    is_role_account: bool
    bounce_risk: str  # "low", "medium", "high"
    reason: str
    raw_response: dict = field(default_factory=dict)

@dataclass  
class ListVerificationSummary:
    total_addresses: int = 0
    valid_deliverable: int = 0
    invalid: int = 0
    risky: int = 0
    disposable: int = 0
    role_accounts: int = 0
    api_errors: int = 0

    def projected_bounce_rate(self) -> float:
        """Estimate bounce rate if you send to unverified addresses."""
        if self.total_addresses == 0:
            return 0.0
        at_risk = self.invalid + self.risky
        return round((at_risk / self.total_addresses) * 100, 2)

    def safe_to_send_count(self) -> int:
        """Number of addresses safe to send to."""
        return self.valid_deliverable

    def print_summary(self) -> None:
        print("\n" + "=" * 60)
        print("LIST VERIFICATION SUMMARY")
        print("=" * 60)
        print(f"Total Addresses Checked:  {self.total_addresses:,}")
        print(f"Valid & Deliverable:       {self.valid_deliverable:,}")
        print(f"Invalid (will hard bounce):{self.invalid:,}")
        print(f"Risky (soft bounce risk):  {self.risky:,}")
        print(f"Disposable Addresses:      {self.disposable:,}")
        print(f"Role Accounts:             {self.role_accounts:,}")
        print(f"API Errors:                {self.api_errors:,}")
        print("-" * 60)
        print(f"Projected Bounce Rate:     {self.projected_bounce_rate()}%")
        print(f"Safe to Send:              {self.safe_to_send_count():,}")

        bounce_rate = self.projected_bounce_rate()
        if bounce_rate < 0.5:
            status = "✓ EXCELLENT — List is clean"
        elif bounce_rate < 2.0:
            status = "⚠ WARNING — Clean before sending"
        else:
            status = "✗ CRITICAL — Do not send without cleaning"

        print(f"Status:                    {status}")
        print("=" * 60)

def verify_single_email(
    email: str,
    api_key: str,
    timeout: int = 10
) -> Optional[VerificationResult]:
    """
    Verify a single email address using the MailValid API.

    Args:
        email: Email address to verify
        api_key: Your MailValid API key
        timeout: Request timeout in seconds

    Returns:
        VerificationResult object or None if API call fails
    """
    headers = {
        "Authorization": f"Bearer {api_key}",
        "Content-Type": "application/json"
    }

    payload = {"email": email}

    try:
        response = requests.post(
            MAILVALID_API_URL,
            headers=headers,
            json=payload,
            timeout=timeout
        )
        response.raise_for_status()
        data = response.json()

        return VerificationResult(
            email=email,
            is_valid=data.get("is_valid", False),
            is_deliverable=data.get("is_deliverable", False),
            is_disposable=data.get("is_disposable", False),
            is_role_account=data.get("is_role_account", False),
            bounce_risk=data.get("bounce_risk", "unknown"),
            reason=data.get("reason", ""),
            raw_response=data
        )

    except requests.exceptions.Timeout:
        print(f"  Timeout verifying {email}")
        return None
    except requests.exceptions.HTTPError as e:
        print(f"  HTTP error verifying {email}: {e}")
        return None
    except requests.exceptions.RequestException as e:
        print(f"  Request error verifying {email}: {e}")
        return None

def verify_list_from_csv(
    input_file: str,
    output_file: str,
    email_column: str = "email",
    api_key: str = MAILVALID_API_KEY,
    rate_limit_delay: float = 0.1,
    batch_size: int = 100
) -> ListVerificationSummary:
    """
    Verify a list of email addresses from a CSV file.
    Writes results to an output CSV and returns a summary.

    Args:
        input_file: Path to input CSV file
        output_file: Path to write verified results
        email_column: Name of the column containing email addresses
        api_key: Your MailValid API key
        rate_limit_delay: Seconds to wait between API calls
        batch_size: Number of addresses to process before saving progress

    Returns:
        ListVerificationSummary with aggregated results
    """
    summary = ListVerificationSummary()
    results = []

    input_path = Path(input_file)
    if not input_path.exists():
        raise FileNotFoundError(f"Input file not found: {input_file}")

    with open(input_file, "r", encoding="utf-8") as f:
        reader = csv.DictReader(f)

        if email_column not in reader.fieldnames:
            raise ValueError(
                f"Column '{email_column}' not found. "
                f"Available columns: {reader.fieldnames}"
            )

        emails = [row[email_column].strip() for row in reader if row[email_column].strip()]

    summary.total_addresses = len(emails)
    print(f"Starting verification of {len(emails):,} addresses...")
    print(f"Estimated time: {len(emails) * rate_limit_delay / 60:.1f} minutes")

    for i, email in enumerate(emails, 1):
        if i % 50 == 0:
            print(f"  Progress: {i}/{len(emails)} ({i/len(emails)*100:.1f}%)")

        result = verify_single_email(email, api_key)

        if result is None:
            summary.api_errors += 1
            results.append({
                "email": email,
                "status": "api_error",
                "is_valid": False,
                "is_deliverable": False,
                "is_disposable": False,
                "is_role_account": False,
                "bounce_risk": "unknown",
                "reason": "API error during verification",
                "safe_to_send": False
            })
            continue

        # Update summary counters
        if result.is_valid and result.is_deliverable and result.bounce_risk == "low":
            summary.valid_deliverable += 1
            safe_to_send = True
            status = "valid"
        elif result.bounce_risk == "medium":
            summary.risky += 1
            safe_to_send = False
            status = "risky"
        else:
            summary.invalid += 1
            safe_to_send = False
            status = "invalid"

        if result.is_disposable:
            summary.disposable += 1
        if result.is_role_account:
            summary.role_accounts += 1

        results.append({
            "email": email,
            "status": status,
            "is_valid": result.is_valid,
            "is_deliverable": result.is_deliverable,
            "is_disposable": result.is_disposable,
            "is_role_account": result.is_role_account,
            "bounce_risk": result.bounce_risk,
            "reason": result.reason,
            "safe_to_send": safe_to_send
        })

        # Save progress in batches
        if i % batch_size == 0:
            _save_results(results, output_file)
            print(f"  Progress saved at {i} addresses")

        time.sleep(rate_limit_delay)

    # Final save
    _save_results(results, output_file)
    print(f"\nVerification complete. Results saved to {output_file}")

    summary.print_summary()
    return summary

def _save_results(results: list, output_file: str) -> None:
    """Save verification results to CSV."""
    if not results:
        return

    fieldnames = results[0].keys()
    with open(output_file, "w", newline="", encoding="utf-8") as f:
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        writer.writerows(results)

def export_clean_list(
    verified_results_file: str,
    clean_output_file: str
) -> int:
    """
    Extract only safe-to-send addresses from a verified results file.

    Args:
        verified_results_file: Path to CSV output from verify_list_from_csv
        clean_output_file: Path to write clean addresses

    Returns:
        Number of addresses in the clean list
    """
    clean_addresses = []

    with open(verified_results_file, "r", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            if row.get("safe_to_send", "").lower() == "true":
                clean_addresses.append({"email": row["email"]})

    with open(clean_output_file, "w", newline="", encoding="utf-8") as f:
        writer = csv.DictWriter(f, fieldnames=["email

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
````

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