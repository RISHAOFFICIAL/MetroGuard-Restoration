# Automation Blueprint — End-to-End Lead Capture & Routing
## MetroGuard Restoration | System Architecture

---

## 1. System Overview

The automation system is designed to be **nearly hands-off** after initial setup. The core loop is:

```
Visitor → Lead Capture → AI Qualification → SMS/Email → Contractor Routing → Payment → Reporting
```

Every step is automated. The operator only intervenes when a lead is disputed or a contractor reports a质量问题 (quality issue).

---

## 2. Lead Flow Diagram

```
[Website/Google/My Business]
        │
        ▼
┌───────────────────────────┐
│   LANDING PAGE / FORM     │
│   (Carrd + Tally Form)    │
└───────────┬───────────────┘
            │
            ▼ (Form Submission OR Phone Call)
┌───────────────────────────┐
│   ZAPIER / MAKE.COM       │
│   Webhook Receiver        │
└───────────┬───────────────┘
            │
            ├──────────────────────────────┐
            │                              │
            ▼                              ▼
┌───────────────────────┐    ┌───────────────────────────┐
│   HUBSPOT CRM         │    │   TWILIO (Missed Call)    │
│   Create Contact      │    │   Auto-Reply SMS          │
│   Add to Pipeline      │    └───────────┬───────────────┘
└───────────┬───────────┘                │
            │                            ▼
            │              ┌───────────────────────────┐
            │              │   AI CHATBOT (Landbot)     │
            │              │   Qualify: Address,        │
            │              │   Emergency?, Insurance?   │
            │              └───────────┬───────────────┘
            │                          │
            └──────────────────────────┤
                                         │
                    ┌────────────────────┼────────────────────┐
                    │                    │                    │
                    ▼                    ▼                    ▼
           ┌───────────────┐  ┌──────────────────┐  ┌─────────────────┐
           │ HOT LEAD      │  │ WARM LEAD        │  │ COLD LEAD       │
           │ (Emergency)   │  │ (Quote Request)  │  │ (Info Seek)     │
           └───────┬───────┘  └────────┬─────────┘  └────────┬────────┘
                   │                    │                     │
                   ▼                    ▼                     ▼
        ┌──────────────────┐  ┌──────────────────┐  ┌─────────────────┐
        │ IMMEDIATE CALL   │  │ EMAIL SEQUENCE   │  │ NURTURE EMAIL   │
        │ TO CONTRACTOR    │  │ 3-Email Drip     │  │ Weekly Tips     │
        │ (Twilio Call     │  │ + SMS Day 1,3,7  │  │ For 30 Days     │
        │  Routing)        │  └──────────────────┘  └─────────────────┘
        └──────────────────┘
                   │
                   ▼
        ┌──────────────────┐
        │ CONTRACTOR       │
        │ NOTIFICATION     │
        │ (SMS + Email)    │
        └────────┬─────────┘
                 │
                 ▼
        ┌──────────────────┐
        │ PAYMENT REQUEST  │
        │ (Wave Invoice)   │
        └──────────────────┘
```

---

## 3. Detailed Automation Triggers

### 3.1 Form Submission Trigger
- **Source:** Tally form on quote page
- **Zapier Action:** Create contact in HubSpot with: Name, Phone, Email, Service Type, City, Urgency Level
- **HubSpot Workflow:** Assign lead score based on urgency (Emergency = 100, Quote = 50, Info = 25)
- **Email:** Send "We received your request" confirmation + "What happens next" email immediately

### 3.2 Phone Call Missed-Call Automation
- **Trigger:** Call not answered within 30 seconds
- **Action 1:** Play pre-recorded message: "Thank you for calling MetroGuard. All operators are currently assisting another caller. Please hold for the next available specialist."
- **Action 2:** After 60 seconds, trigger SMS: "Thanks for calling MetroGuard! We've dispatched a specialist to your area. They'll call you back within 5 minutes. For immediate emergency help, please call (contractor's direct line)."
- **Zapier:** Log call attempt in HubSpot with timestamp and duration

### 3.3 AI Chatbot Qualification Flow
When visitor engages with Landbot on the quote page:

```
Step 1: "Hi! I'm your MetroGuard assistant. What's your name?" → Save Name
Step 2: "What's your zip code?" → Save ZIP (validates Metro Detroit coverage)
Step 3: "Is this an emergency? (Yes/No)" → If YES → Flag as HOT LEAD
Step 4: "Are you filing an insurance claim? (Yes/No/Maybe)" → Save insurance status
Step 5: "What's your phone number?" → Save Phone
Step 6: "Great! A MetroGuard specialist will call you within 5 minutes."
```

**Qualification Rules:**
- ZIP outside Metro Detroit → "We don't serve your area yet. Here's a partner referral." (Non-converting)
- Emergency = YES → Route to contractor within 2 minutes (SMS + Call)
- Insurance = YES → Add "Insurance" tag in HubSpot for contractor follow-up
- Non-emergency → Add to 3-day email nurture sequence

### 3.4 Contractor Notification
When a HOT LEAD is captured:
- **SMS to Contractor:** "[City] Emergency Lead | [Name] | [Phone] | [ZIP] | Insurance: [Y/N] | Link to CRM"
- **Email to Contractor:** Same info in HTML format with one-click callback link
- **CRM Note:** "Dispatched to [Contractor Name] at [Time]. Callback deadline: [Time + 5min]"

### 3.5 Email Nurture Sequence (Warm Leads)
For non-emergency quote requests:
- **Email 1 (Day 0):** "Thanks [Name]! We've received your request."
- **Email 2 (Day 1):** "What to expect from MetroGuard" — trust building, process explanation
- **Email 3 (Day 3):** "Did you know? 5 signs your basement needs waterproofing" — educational, CTA to book
- **Email 4 (Day 7):** "Special offer: Free inspection for [City] residents this week"
- **SMS on Day 2:** "Hi [Name], any questions? We're here to help. Reply STOP to unsubscribe."

### 3.6 Follow-Up for Cold Leads
- If no response after 14 days → Send "We're still here" email with new offer
- If no response after 30 days → Move to "Dormant" list in HubSpot
- Dormant leads get a monthly "Seasonal Tips" email (fall basement prep, winter thaw, spring flooding)

---

## 4. Lead Scoring Model

| Signal | Points | Notes |
| :--- | :--- | :--- |
| Emergency = YES | +100 | Immediate contractor dispatch |
| Insurance = YES | +50 | Higher close rate, faster decision |
| ZIP in Metro Detroit | +30 | Valid service area |
| Phone number provided | +20 | Direct contact ability |
| Form submission (vs. call) | +10 | Higher intent |
| After-hours submission | +15 | Less competition, urgent need |
| Property type = House | +10 | Larger job potential |
| Has described problem | +10 | Pre-qualified need |

**Score Tiers:**
- **0-40:** Cold — Nurture email sequence
- **41-80:** Warm — 3-day follow-up sequence + SMS
- **81-150:** Hot — Immediate contractor dispatch
- **150+:** Emergency VIP — All hands on deck, manager notified

---

## 5. CRM Pipeline Configuration

HubSpot Pipeline: **MetroGuard Lead Pipeline**

| Stage | Description | Auto-Actions |
| :--- | :--- | :--- |
| **New Lead** | Just captured | Send confirmation email, assign lead score |
| **Qualified** | Score > 40 | Add to appropriate sequence (Hot/Warm/Cold) |
| **Dispatched** | Sent to contractor | Notify contractor via SMS + email |
| **Contacted** | Contractor called | Log in CRM, start "Follow-up" timer |
| **Quoted** | Contractor gave quote | Track quote amount in CRM |
| **Closed-Won** | Job closed | Mark as won, update contractor payment due |
| **Closed-Lost** | No conversion | Move to dormant, continue nurture |

---

## 6. Reporting Dashboard

### Key Metrics (Track Weekly):

| Metric | Target | How to Measure |
| :--- | :--- | :--- |
| Leads Captured | 10-40/mo | HubSpot + Google Analytics |
| Emergency Lead % | 20-30% | Lead score distribution |
| Contractor Response Time | < 5 min | HubSpot timestamp comparison |
| Lead-to-Quote Rate | 60%+ | Contractor feedback logged |
| Conversion to Payment | 40-60% | Wave invoices |
| Cost Per Lead | < $15 | (Monthly spend / Leads) |
| Revenue Per Lead | $150-450 | Actual PPL revenue / Leads |

### Monthly Report Automation:
- HubSpot + Make.com integration to generate weekly email report
- Report includes: Lead volume, source breakdown, avg lead score, contractor performance scores, revenue

---

## 7. Contractor Management Automation

### Onboarding New Contractor:
1. Send onboarding email with login to shared HubSpot view
2. SMS with "Reply YES to receive emergency lead alerts"
3. Set up Twilio routing to their phone number
4. Add them to the "Metro Detroit Contractors" Slack channel

### Contractor Performance Tracking:
- **Response Rate:** % of leads they call back within 5 minutes
- **Quote Rate:** % of leads they submit a quote for
- **Close Rate:** % of quoted leads that become jobs
- **Avg Job Value:** Average $ of jobs they close

**Monthly Review:** Email contractor a summary of their performance + suggestions for improvement.

---

## 8. Implementation Sequence

### Day 1-3: Core Automation
1. Set up HubSpot CRM with pipeline and lead scoring
2. Configure Tally form → Zapier → HubSpot integration
3. Set up Twilio missed-call SMS automation

### Day 4-7: AI & Qualification
4. Build Landbot chatbot flow (8 questions for qualification)
5. Connect chatbot to HubSpot lead scoring
6. Set up contractor notification workflow

### Week 2: Nurture & Reporting
7. Build email nurture sequences (Hot, Warm, Cold)
8. Set up SMS follow-up sequences
9. Build HubSpot dashboard for weekly reporting

### Week 3-4: Testing & Optimization
10. Test full flow with a test lead
11. Adjust lead scoring weights based on actual conversion data
12. Optimize chatbot questions based on qualification effectiveness

---

## 9. System Failures & Fallbacks

| Failure Scenario | Fallback |
| :--- | :--- |
| Twilio goes down | Calls forward directly to operator phone |
| HubSpot is down | Leads sent to email + Slack channel |
| Chatbot fails | Form fallback + "Call us now" button |
| Contractor doesn't answer | Escalate to backup contractor + alert manager |
| Form spam | CAPTCHA + honeypot field to filter bots |

---

## 10. Tools Summary for Automation

| Purpose | Tool |
| :--- | :--- |
| Form → CRM | Zapier / Make.com |
| CRM | HubSpot Free |
| Phone/SMS | Twilio |
| AI Chatbot | Landbot |
| Email Sequences | HubSpot (free) |
| Contractor Routing | Twilio + Zapier |
| Invoicing | Wave |
| Reporting | HubSpot + Make.com |

**Total monthly cost for automation layer:** ~$50/mo (Twilio ~$20, Make.com ~$10, Landbot ~$30)

This automation system creates a true "set and forget" lead generation machine that captures, qualifies, routes, and reports on leads 24/7 without manual intervention.