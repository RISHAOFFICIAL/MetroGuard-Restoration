# Automation Setup Status — Fully Free Stack
## MetroGuard Restoration | Implementation Guide

**STACK:** Tally Forms + HubSpot Free CRM + Google Voice + Make.com (free tier)
**MONTHLY COST: ~$1 (domain only)**
**NO LANDNOT — FREE ALTERNATIVES USED INSTEAD**

---

## Overview

This guide sets up the complete automation system using 100% free tools. No paid chatbot, no monthly software costs beyond the domain name.

---

## 1. HubSpot CRM Setup (Free)

### Step 1.1: Create HubSpot Account
1. Go to [hubspot.com](https://hubspot.com) → "Get Started Free"
2. Use a professional email
3. Company name: **MetroGuard Restoration**
4. Industry: **Home Services**

### Step 1.2: Create MetroGuard Lead Pipeline
1. Go to **Settings → Objects → Deals → Pipelines**
2. Click **Create Pipeline**
3. Name: **MetroGuard Lead Pipeline**
4. Add stages:
   - **New Lead** (default)
   - **Qualified** (lead score > 40)
   - **Dispatched** (sent to contractor)
   - **Contacted** (contractor called)
   - **Quoted** (quote submitted)
   - **Closed-Won** (job won)
   - **Closed-Lost** (no conversion)

### Step 1.3: Create Lead Scoring Properties
1. Go to **Settings → Properties → Contacts**
2. Create number properties:
   - `lead_score` (default: 0)
   - `is_emergency` (yes/no)
   - `has_insurance` (yes/no)
   - `zip_valid` (yes/no)
   - `urgency_tier` (Emergency/Hot/Warm/Cold)

### Step 1.4: Create Automation Workflows
1. Go to **Automation → Workflows → Create workflow**
2. Trigger: **Contact property change** (when lead_score updates)
3. Add branches:
   - IF `lead_score` >= 100 → Tag "Emergency VIP", Send SMS to contractor
   - IF `lead_score` >= 81 → Tag "Hot Lead", Send contractor notification
   - IF `lead_score` >= 41 → Tag "Warm Lead", Enroll in nurture sequence
   - IF `lead_score` < 41 → Tag "Cold Lead", Weekly tips email

### Step 1.5: Enable HubSpot Conversations (Free Chat Widget)
1. Go to **Conversations → Inbox**
2. Click **Get started** with the free Conversations tool
3. Go to **Settings → Channels → Website** → Generate embed code
4. Add the embed code to your website (or WordPress header)
5. Customize the pre-chat form: capture name, email, message

---

## 2. Google Voice Setup (Free)

### Step 2.1: Create Google Voice Account
1. Go to [voice.google.com](https://voice.google.com)
2. Sign in with a Google account
3. Search for a Detroit area code number (313, 586, 248)
4. Click **Select** to claim the number

### Step 2.2: Configure Voicemail + SMS Auto-Reply
1. In Google Voice → **Settings → Voicemail**
2. Record a greeting: "Thank you for calling MetroGuard Restoration. All operators are currently assisting another caller. Please hold for the next available specialist."
3. Enable **Voicemail transcription** (free)
4. Under **SMS replies** → Set auto-reply: "Thanks for calling MetroGuard! We'll have a specialist call you back within 5 minutes. For immediate emergency help, please visit our website."

### Step 2.3: Set Up Call Forwarding
1. Go to **Settings → Calls → Forward calls**
2. Forward to your personal mobile number
3. Set "Ring simultaneously" or sequential ring with your phone first

---

## 3. Tally Forms + Make.com Integration

### Step 3.1: Create Tally Form
1. Go to [tally.so](https://tally.so) → "Sign Up Free"
2. Create form: **MetroGuard Quote Request**
3. Add fields:
   - Full Name (required)
   - Phone Number (required)
   - Email (required)
   - Zip Code (required)
   - Service Needed (dropdown: Water Damage / Basement Waterproofing / Mold Remediation / Foundation Repair / Other)
   - Is this an emergency? (Yes/No)
   - Are you filing an insurance claim? (Yes/No/Maybe)
   - Describe the issue (textarea, optional)
4. Customize thank you message: "Thank you! A MetroGuard specialist will contact you within 5 minutes."

### Step 3.2: Set Up Make.com Webhook
1. Go to [make.com](https://make.com) → "Get Started Free"
2. Create new scenario: **MetroGuard Lead Router**
3. Add module: **Webhooks → Custom Webhook**
4. Click **Copy** to copy the webhook URL
5. In Tally form → **Integrations → Webhooks** → Paste webhook URL
6. Test by submitting a sample form entry

### Step 3.3: Connect Make.com to HubSpot
1. Add module: **HubSpot → Create Contact**
2. Map fields:
   - email → Email
   - name → Full Name
   - phone → Phone Number
   - service_type → Service Needed
   - is_emergency → Is this an emergency?
   - has_insurance → Are you filing an insurance claim?
   - zip → Zip Code
3. Add a **Router** module to branch by lead score

### Step 3.4: Build Lead Routing Automation
```
TRIGGER: Webhook receives lead from Tally
  │
  ├─ Condition: is_emergency = YES
  │    ├─ HubSpot: Create contact + score +100
  │    ├─ HubSpot: Add tag "Hot Lead" + "Emergency VIP"
  │    └─ SMS to contractor: "[City] EMERGENCY | [Name] | [Phone] | [ZIP]"
  │
  ├─ Condition: has_insurance = YES AND is_emergency = NO
  │    ├─ HubSpot: Create contact + score +50
  │    ├─ HubSpot: Add tag "Warm Lead"
  │    └─ Enroll in 3-day email nurture
  │
  └─ Condition: All others
       ├─ HubSpot: Create contact + score 20-30
       ├─ HubSpot: Add tag "Cold Lead"
       └─ Enroll in weekly tips sequence
```

---

## 4. HubSpot Conversations (取代 Landbot — 免费)

### Step 4.1: Enable the Free Chat Widget
1. In HubSpot → **Conversations → Inbox → Settings**
2. Go to **Channels → Website**
3. Click **Install** to get the tracking code
4. Paste into WordPress site header or HTML site footer
5. Customize the widget appearance (MetroGuard colors: Navy + Steel Silver)

### Step 4.2: Configure Pre-Chat Form
1. In HubSpot → **Conversations → Settings → Pre-chat forms**
2. Enable pre-chat form capture
3. Add fields: Name, Email, Phone, Message
4. Set to: "Always show" the chat button

### Step 4.3: Set Up Routing Rules
1. In HubSpot → **Conversations → Routing**
2. Route all new conversations to: assign to yourself initially
3. Set auto-reply: "Hi! Thanks for reaching out to MetroGuard. A specialist will be with you within 5 minutes. For emergencies, please call us directly."

---

## 5. Contractor Notification Setup

### Step 5.1: SMS to Contractor (Make.com Free)
Create this template in Make.com:
```
SMS to Contractor:
[METROGUARD LEAD] Emergency in [City]!
Name: [Name]
Phone: [Phone]
ZIP: [ZIP]
Insurance: [Y/N]
Service: [Service]
Score: [Score]
Respond within 5 minutes.
```

### Step 5.2: Email to Contractor
```
Subject: New Lead - [City] - Emergency: [Y/N]
MetroGuard has a new [Service] lead in [City].
Name: [Name] | Phone: [Phone] | ZIP: [ZIP]
Emergency: [Y/N] | Insurance: [Y/N]
Lead Score: [Score]
[View in HubSpot → link]
```

---

## 6. Testing Checklist

### Phone / Google Voice:
- [ ] Call the Google Voice number from mobile
- [ ] Verify voicemail greeting plays
- [ ] Verify auto-reply SMS is received
- [ ] Verify call forwarding works

### Form / Make.com / HubSpot:
- [ ] Submit test Tally form entry
- [ ] Verify webhook fires in Make.com
- [ ] Verify contact appears in HubSpot
- [ ] Verify lead scoring calculates correctly
- [ ] Verify Hot leads get "Emergency VIP" tag

### HubSpot Conversations Widget:
- [ ] Visit website, click chat widget
- [ ] Complete pre-chat form
- [ ] Verify data appears in HubSpot inbox

### Make.com Routing:
- [ ] Submit emergency form → verify contractor SMS sent within 2 minutes
- [ ] Submit warm form → verify email sequence starts
- [ ] Submit cold form → verify weekly tips sequence starts

---

## 7. Monthly Maintenance

| Task | Frequency |
| :--- | :--- |
| Review lead scores | Weekly |
| Check contractor response times | Weekly |
| Clean HubSpot pipeline | Monthly |
| Update Google Voice voicemail | As needed |
| Check for spam leads | Weekly |

---

## 8. Quick Start Summary (Day 1-3)

### Day 1:
- [ ] Create HubSpot account + pipeline + lead scoring properties
- [ ] Create Tally form and connect to Make.com webhook

### Day 2:
- [ ] Set up Google Voice number + voicemail greeting + SMS auto-reply
- [ ] Set up Make.com scenario: Tally → HubSpot → Contractor routing

### Day 3:
- [ ] Enable HubSpot Conversations widget (free chat)
- [ ] Build email nurture sequences in HubSpot
- [ ] Test full flow end-to-end

---

## Status

**Stack: 100% free. Total monthly cost: ~$1/mo (domain only)**
**No Landbot, no Twilio, no paid chatbot required.**
**Setup time: 3-5 days for a non-technical operator.**
