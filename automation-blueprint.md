# Automation Blueprint — End-to-End Lead Capture & Routing
## MetroGuard Restoration | System Architecture (FREE STACK)

---

## 1. System Overview

The automation system is designed to be **fully hands-off** after initial setup — using entirely free tools. No Landbot, no paid chatbot.

**Core Loop:**
```
Visitor → Tally Form → HubSpot CRM → Lead Scoring → Hot/Warm/Cold Routing → Contractor SMS/Email → Wave Invoice → Report
```

**Free Stack:** Tally Forms + HubSpot CRM + Google Voice + Make.com (free tier)

---

## 2. Lead Flow Diagram

```
[Website / Google Business Profile]
        │
        ▼
┌───────────────────────────┐
│   TALLY FORM              │
│   (conversational style)  │
│   6 questions: name, ZIP, │
│   emergency, insurance, │
│   phone, issue            │
└───────────┬───────────────┘
            │
            ▼ (Form Submission)
┌───────────────────────────┐
│   MAKE.COM WEBHOOK        │
│   Receive + Route          │
└───────────┬───────────────┘
            │
            ├──────────────────────────────┐
            │                              │
            ▼                              ▼
┌───────────────────────┐    ┌───────────────────────────┐
│   HUBSPOT CRM         │    │   GOOGLE VOICE            │
│   Create Contact      │    │   Missed Call SMS         │
│   Lead Score         │    │   Auto-reply               │
└───────────┬───────────┘    └───────────────────────────┘
            │
            ▼
┌───────────────────────────┐
│   HUBSPOT LEAD SCORING    │
│   Emergency = 100 pts     │
│   Insurance = 50 pts      │
│   Valid ZIP = 30 pts     │
│   Phone = 20 pts          │
└───────────┬───────────────┘
            │
            ├──────────────────────────────────┐
            │                                  │
            ▼                                  ▼
┌───────────────────────┐         ┌───────────────────────┐
│   HOT LEAD (81+)       │         │   WARM LEAD (41-80)   │
│   Emergency / High    │         │   Quote Request        │
│   Score               │         │                       │
└───────────┬───────────┘         └───────────┬───────────┘
            │                                  │
            ▼                                  ▼
┌───────────────────────┐         ┌───────────────────────┐
│   MAKE.COM            │         │   HUBSPOT EMAIL        │
│   SMS to Contractor   │         │   3-Day Nurture        │
│   within 2 minutes   │         │   Sequence             │
└───────────┬───────────┘         └───────────────────────┘
            │
            ▼
┌───────────────────────┐
│   COLD LEAD (<41)     │
│   Info-seeker         │
└───────────┬───────────┘
            ▼
┌───────────────────────┐
│   HUBSPOT EMAIL        │
│   Weekly Tips         │
│   (30-day nurture)     │
└───────────────────────┘
            │
            ▼
┌───────────────────────┐
│   CONTRACTOR          │
│   SMS + Email notify  │
│   (Make.com routing)  │
└───────────┬───────────┘
            ▼
┌───────────────────────┐
│   WAVE INVOICE         │
│   Payment request      │
└───────────────────────┘
```

---

## 3. Detailed Automation Triggers

### 3.1 Tally Form Submission
- **Form:** Conversational Tally form with 6 questions (name, ZIP, emergency Y/N, insurance Y/N, phone, issue description)
- **Make.com:** Receives webhook from Tally → creates HubSpot contact → assigns lead score
- **HubSpot:** Instantly tags Hot/Warm/Cold based on score

### 3.2 Google Voice Missed-Call SMS
- **Trigger:** Call not answered → goes to Google Voice voicemail
- **Auto-reply:** Google Voice sends automatic SMS: "Thanks for calling MetroGuard! We'll have a specialist call you back within 5 minutes. For immediate help, please call our emergency line."
- **Make.com:** Watches for new Google Voice contacts → creates HubSpot record

### 3.3 Lead Qualification (Free — No Chatbot)

**Instead of a paid chatbot, use Tally's conversational forms + HubSpot Conversations:**

**Tally Form Flow:**
```
Q1: "What's your name?" → Save name
Q2: "What's your zip code?" → Validates service area (313, 586, 248 area codes)
Q3: "Is this an emergency? (Yes = immediate response)" → If YES → Tag HOT LEAD
Q4: "Are you filing an insurance claim?" → Save insurance status
Q5: "What's your phone number?" → Required for contractor callback
Q6: "Describe your issue briefly" → Save for contractor context
```

**HubSpot Conversations Widget (free embed):**
- One-line JavaScript embed on the site
- Pre-chat form: captures name, email, message
- Routes directly to HubSpot as a new lead
- Always-on, zero cost

**Qualification Rules in HubSpot:**
- ZIP outside Metro Detroit → Auto-tag "Out of Area" → no contractor routing
- Emergency = YES → Score +100 → Tag "HOT LEAD" → Make.com SMS to contractor within 2 min
- Insurance = YES → Score +50 → faster contractor response priority
- Non-emergency → Score 41-80 → Warm → 3-day email nurture
- Info-seeker → Score <41 → Cold → weekly tips nurture

### 3.4 Contractor Notification
When a HOT LEAD is created in HubSpot:
- **Make.com:** Triggers SMS to contractor: "[City] Emergency Lead | [Name] | [Phone] | [ZIP] | Insurance: [Y/N]"
- **Make.com:** Sends email to contractor with full lead details + one-click callback link
- **CRM Note:** "Dispatched to [Contractor] at [Time]. Callback deadline: [Time + 5min]"

### 3.5 Email Nurture Sequences (HubSpot Free)
**Hot Lead Sequence (3 emails over 3 days):**
- Day 0: "Thanks [Name]! We're on it."
- Day 1: "What happens next — our process"
- Day 3: "Special offer: Free inspection this week"

**Warm Lead Sequence (4 emails over 7 days):**
- Day 0: "Thanks for contacting MetroGuard"
- Day 2: "5 signs your [service] needs attention"
- Day 5: "Did you know? [Local flooding tip]"
- Day 7: "Limited time offer"

**Cold Lead Sequence (weekly for 30 days):**
- Week 1: "5 basement prep tips for Metro Detroit winter"
- Week 2: "Seasonal flooding guide"
- Week 3: "Insurance claim tips"
- Week 4: "Final offer — Free consultation"

### 3.6 SMS Automation (Make.com Free Tier)
- **Make.com free tier:** 1,000 operations/mo — enough for 30-50 leads
- **SMS to contractor:** Hot leads trigger SMS immediately
- **SMS to lead:** Confirmation SMS: "Hi [Name], MetroGuard specialist will call you within 5 minutes."

---

## 4. Lead Scoring Model

| Signal | Points | Action |
| :--- | :--- | :--- |
| Emergency = YES | +100 | Immediate contractor dispatch |
| Insurance = YES | +50 | Faster close, higher value |
| ZIP in Metro Detroit | +30 | Valid service area |
| Phone provided | +20 | Direct contact ability |
| Form submission | +10 | Higher intent than call |
| After-hours | +15 | Less competition |
| Has described issue | +10 | Pre-qualified need |

**Score Tiers:**
- **0–40:** Cold → Weekly nurture email
- **41–80:** Warm → 3-day email sequence
- **81–150:** Hot → Immediate contractor SMS + call
- **150+:** Emergency VIP → SMS + call + manager alert

---

## 5. CRM Pipeline Configuration (HubSpot Free)

| Stage | Auto-Actions |
| :--- | :--- |
| **New Lead** | Confirm email sent, lead score calculated |
| **Qualified** | Enrolled in Hot/Warm/Cold sequence |
| **Dispatched** | Contractor notified via Make.com SMS |
| **Contacted** | Contractor logs call in CRM |
| **Quoted** | Quote amount tracked |
| **Closed-Won** | Wave invoice sent |
| **Closed-Lost** | Dormant nurture continues |

---

## 6. Reporting Dashboard

| Metric | Target | Tool |
| :--- | :--- | :--- |
| Leads captured | 10–40/mo | HubSpot |
| Emergency lead % | 20–30% | Lead score distribution |
| Contractor response | < 5 min | HubSpot timestamps |
| Lead-to-quote rate | 60%+ | Contractor self-report |
| Revenue per lead | $150–450 | Wave invoices |
| Cost per lead | **$0** | Stack is free! |

---

## 7. Implementation Sequence

### Day 1-3:
1. Set up HubSpot CRM + pipeline + lead scoring properties
2. Create Tally form with 6 questions + webhook to Make.com
3. Connect Make.com: Tally → HubSpot contact creation
4. Set up Google Voice number + voicemail/SMS auto-reply

### Day 4-7:
5. Configure Make.com: Hot Lead → contractor SMS routing
6. Build email nurture sequences (Hot/Warm/Cold) in HubSpot
7. Enable HubSpot Conversations widget (free embed)
8. Test full flow: submit form → check HubSpot → verify contractor SMS

### Week 2:
9. Add Google Business Profile optimization
10. Set up Google Analytics 4 goals for form submissions
11. Finalize contractor onboarding (SMS opt-in)

---

## 8. System Failovers

| Failure | Fallback |
| :--- | :--- |
| Tally down | HTML form fallback → email to operator |
| HubSpot down | Leads go to Make.com → backup email |
| Google Voice down | Calls forward directly to operator mobile |
| Contractor no answer | Escalate to backup contractor + alert |

---

## 9. Tools Summary — FREE STACK

| Purpose | Tool | Cost |
| :--- | :--- | :--- |
| Forms (qualification) | Tally Forms | $0 |
| CRM + email sequences | HubSpot Free | $0 |
| Lead scoring | HubSpot Free | $0 |
| Chat widget | HubSpot Conversations | $0 |
| Phone + voicemail | Google Voice | $0 |
| SMS automation | Make.com free | $0 |
| Contractor routing | Make.com free | $0 |
| Invoicing | Wave | $0 |
| Hosting | GitHub Pages | $0 |
| **TOTAL** | | **~$1/mo** |

This automation system runs 100% on free tools — zero monthly software cost.
