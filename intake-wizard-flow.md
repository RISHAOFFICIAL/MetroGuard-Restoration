# MetroGuard Emergency Intake Wizard - Logic & Question Set

This 8-question flow is designed for the HubSpot Conversations widget (free chat) or Tally Forms. It triages leads based on urgency and insurance status to ensure 15-minute callback priority for Critical (Tier 1) leads.

## 1. The Logic (Lead Scoring)
| Condition | Points |
| :--- | :--- |
| Standing Water Present | +100 |
| Active Leak / Burst Pipe | +50 |
| Homeowner Insured | +50 |
| Valid Metro Detroit ZIP | +30 |
| Commercial Property | +20 |

**Tiers:**
*   **Tier 1 — CRITICAL (Score 150+):** Standing water + Insured. Trigger immediate contractor SMS.
*   **Tier 2 — HIGH (Score 81–149):** Active leak but no standing water yet, or info-seeking but insured.
*   **Tier 3 — STANDARD (Score 41–80):** Minor moisture issues, no insurance, or outside primary ZIPs.

## 2. The 8-Question Flow

1.  **"Hi! I'm the MetroGuard Assistant. What's your name?"**
    *   (Input: Name)
2.  **"Nice to meet you, [Name]. Is this an active water emergency? (e.g., basement flooding, burst pipe)"**
    *   (Options: Yes / No) -> *If Yes, add 50 pts*
3.  **"Is there standing water in the building right now?"**
    *   (Options: Yes / No) -> *If Yes, add 100 pts*
4.  **"What is the ZIP code where service is needed?"**
    *   (Input: ZIP) -> *If in Metro Detroit list, add 30 pts*
5.  **"Do you have homeowners or commercial insurance coverage for this property?"**
    *   (Options: Yes / No / Unsure) -> *If Yes, add 50 pts*
6.  **"What is the best phone number for a specialist to call you in 15 minutes?"**
    *   (Input: Phone)
7.  **"Briefly, what happened? (e.g., 'Sump pump failed', 'Dishwasher leaked')"**
    *   (Input: Text)
8.  **"Is this a residential or commercial property?"**
    *   (Options: Residential / Commercial) -> *If Commercial, add 20 pts*

## 3. Post-Capture Actions
*   **Score > 150:** Display: *"URGENT: Your lead has been prioritized. A specialist will call you within 15 minutes."*
*   **Score < 150:** Display: *"Thank you. A specialist will review your info and contact you shortly (usually within 1 hour)."*

## 4. Routing (Make.com Logic)
*   **Tier 1:** Send SMS to Ready-Response Partner immediately.
*   **Tier 2/3:** Add to HubSpot 'New Lead' pipeline and send internal email notification.
