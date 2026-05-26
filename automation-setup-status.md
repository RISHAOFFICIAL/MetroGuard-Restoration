# Automation Setup Status — HubSpot, Twilio, Landbot & Zapier
## MetroGuard Restoration | Implementation Guide

---

## Overview

This document provides step-by-step setup instructions for the automation infrastructure. Since we cannot access external APIs directly, this guide serves as the **implementation playbook** to hand off to an operator or set up manually.

---

## 1. HubSpot CRM Setup

### Step 1.1: Create HubSpot Account
1. Go to [hubspot.com](https://hubspot.com) → "Get Started Free"
2. Use a professional email (not Gmail if possible)
3. Company name: **MetroGuard Restoration**
4. Industry: **Home Services**
5. Company size: **1-10 employees**

### Step 1.2: Create MetroGuard Lead Pipeline
1. Go to **Settings** → **Objects** → **Deals** → **Pipelines**
2. Click **Create Pipeline**
3. Name: **MetroGuard Lead Pipeline**
4. Add stages:
   - **New Lead** (default stage)
   - **Qualified** (lead score > 40)
   - **Dispatched** (sent to contractor)
   - **Contacted** (contractor called)
   - **Quoted** (contractor submitted quote)
   - **Closed-Won** (job won)
   - **Closed-Lost** (no conversion)

### Step 1.3: Create Lead Scoring Properties
1. Go to **Settings** → **Properties** → **Contacts**
2. Create the following number properties:
   - `lead_score` (default: 0)
   - `is_emergency` (yes/no)
   - `has_insurance` (yes/no)
   - `zip_valid` (yes/no)
   - `contact_method` (phone/form)
   - `urgency_tier` (Emergency/Hot/Warm/Cold)

### Step 1.4: Create Automation Workflow
1. Go to **Automation** → **Workflows** → **Create workflow**
2. Trigger: **Contact property change** (when lead_score updates)
3. Add branches:
   - IF `lead_score` >= 100 → Tag as "Emergency VIP", Send SMS to contractor
   - IF `lead_score` >= 81 → Tag as "Hot Lead", Send contractor notification
   - IF `lead_score` >= 41 → Tag as "Warm Lead", Enroll in nurture sequence
   - IF `lead_score` < 41 → Tag as "Cold Lead", Weekly tips email

---

## 2. Twilio Setup

### Step 2.1: Create Twilio Account
1. Go to [twilio.com](https://twilio.com) → "Sign Up Free"
2. Use work email
3. Verify email and phone number
4. Go to Console → get **Account SID** and **Auth Token**

### Step 2.2: Purchase a Phone Number
1. Go to **Console** → **Phone Numbers** → **Buy a number**
2. Search for a **Detroit area code (313, 586, 248)**
3. Purchase number ($1-2/month)
4. This becomes the **MetroGuard main line** for call tracking

### Step 2.3: Set Up Missed-Call SMS Automation
1. Go to **Console** → **Functions** → **Create**
2. Create a function called `missed-call-reply`:
   ```javascript
   exports.handler = function(context, event, callback) {
     let twiml = new Twilio.twiml.VoiceResponse();
     twiml.say("Thank you for calling MetroGuard Restoration. All operators are currently assisting another caller. Please hold for the next available specialist.");
     
     // Wait 60 seconds then hang up
     twiml.pause({ length: 60 });
     twiml.hangup();
     
     callback(null, twiml);
   };
   ```
3. Set up **Studio Flow** for call handling:
   - When call comes in → Ring main operator phone
   - If no answer in 30 seconds → Play greeting
   - If caller stays → Transfer to on-call specialist
   - If caller hangs up → Trigger SMS

### Step 2.4: Configure SMS Auto-Reply
1. Go to **Console** → **Functions** → **Create**
2. Create function `auto-reply-sms`:
   ```javascript
   exports.handler = function(context, event, callback) {
     const client = context.getTwilioClient();
     const message = event.Body;
     
     client.messages.create({
       to: event.From,
       from: process.env.TWILIO_NUMBER,
       body: "Thanks for calling MetroGuard! We've dispatched a specialist to your area. They'll call you back within 5 minutes. For immediate help, please call our emergency line."
     }).then(() => {
       callback(null, "SMS sent");
     });
   };
   ```

### Step 2.5: Set Up Contractor Routing
1. Go to **Console** → **Phone Numbers** → Select main number
2. Configure **Voice** settings:
   - Accept calls: Yes
   - Heartbeat URL: Your operator's phone (forwarding)
3. Set up **Concurrent calls = 1** to ensure leads don't hit busy signals

---

## 3. Tally Forms + Zapier Integration

### Step 3.1: Create Tally Form
1. Go to [tally.so](https://tally.so) → "Sign Up Free"
2. Create a new form called **"MetroGuard Quote Request"**
3. Add fields:
   - Full Name (required)
   - Phone Number (required)
   - Email (required)
   - Service Needed (dropdown: Water Damage, Basement Waterproofing, Mold Remediation, Foundation Repair, Other)
   - Is this an emergency? (yes/no)
   - Are you filing an insurance claim? (yes/no/maybe)
   - Describe the issue (textarea, optional)
   - Address/ZIP (required)
4. Customize thank you message: "Thank you [Name]! A MetroGuard specialist will contact you within 5 minutes."

### Step 3.2: Set Up Zapier Webhook
1. Go to [zapier.com](https://zapier.com) → Create free account
2. Create new Zap: **Webhooks → Catch Hook**
3. Copy the webhook URL
4. In Tally form → **Integrations** → **Webhooks** → Add webhook URL
5. Set trigger: When form is submitted → Send webhook
6. Test by submitting a test form entry

### Step 3.3: Connect Webhook to HubSpot
1. In Zapier → Add step: **HubSpot → Create Contact**
2. Map fields:
   - email → Email
   - name → Full Name
   - phone → Phone Number
   - service_type → Service Needed
   - is_emergency → Is this an emergency?
   - has_insurance → Are you filing an insurance claim?
   - address → Address/ZIP
3. Add lead scoring calculation in HubSpot based on submitted data

---

## 4. Landbot AI Chatbot Setup

### Step 4.1: Create Landbot Account
1. Go to [landbot.io](https://landbot.io) → "Start Free Trial"
2. Create new bot: **MetroGuard Virtual Assistant**
3. Choose template: **Lead Generation Bot**

### Step 4.2: Build Qualification Flow
Create the following conversation blocks:

```
WELCOME BLOCK:
"Hi! I'm your MetroGuard assistant. How can I help you today?"
[Options: Get a Quote | Emergency Service | General Question]

QUOTE PATH:
Q1: "What's your name?" → Save to {{name}}
Q2: "What's your zip code?" → Save to {{zip}}
Q3: "Is this an emergency? (Yes = immediate response)" → If YES → Tag Emergency
Q4: "Are you filing an insurance claim?" → Save insurance status
Q5: "What service do you need?" → [Water Damage / Basement Waterproofing / Mold / Foundation]
Q6: "What's your phone number?" → Save to {{phone}}
Q7: "Describe your issue briefly" → Save to {{issue}}
Q8: "Great! A MetroGuard specialist will call you within 5 minutes."

ROUTING:
- Emergency = YES → Send to contractor SMS immediately
- Non-emergency → Send confirmation email + 3-day nurture
```

### Step 4.3: Configure Integration to HubSpot
1. In Landbot → **Integrations** → **Zapier**
2. Create zap: **Landbot → HubSpot Contact**
3. Map chatbot responses to HubSpot contact properties
4. Trigger lead scoring in HubSpot based on responses

### Step 4.4: Add to Website
1. In Landbot → **Share** → **Website Widget**
2. Copy embed code
3. Add to WordPress site via Elementor or header.php
4. Position: Bottom-right corner, "Chat with us" button

---

## 5. Make.com Automation Setup

### Step 5.1: Create Make.com Account
1. Go to [make.com](https://make.com) → "Get Started Free"
2. Create new scenario: **MetroGuard Lead Router**

### Step 5.2: Build Hot Lead Routing Automation
```
TRIGGER: Webhook receives lead data from Tally/Zapier
  │
  ├─ Condition: lead_score >= 100 (Emergency)
  │    ├─ Action: Send SMS via Twilio to contractor (format: "[City] EMERGENCY | [Name] | [Phone] | [ZIP]")
  │    ├─ Action: Send email to contractor with lead details
  │    └─ Action: Update HubSpot: Stage = "Dispatched", Tag = "Emergency VIP"
  │
  ├─ Condition: lead_score 81-99 (Hot)
  │    ├─ Action: Send SMS to contractor
  │    ├─ Action: Update HubSpot: Stage = "Qualified"
  │    └─ Action: Start 3-day follow-up sequence
  │
  ├─ Condition: lead_score 41-80 (Warm)
  │    ├─ Action: Update HubSpot: Stage = "New Lead", Tag = "Warm"
  │    └─ Action: Enroll in email nurture sequence
  │
  └─ Condition: lead_score < 41 (Cold)
       └─ Action: Update HubSpot: Tag = "Cold", Start weekly tips sequence
```

### Step 5.3: Set Up Contractor Notification Template
Create the following SMS/email template:
```
SMS:
[METROGUARD LEAD] City: [City] | Name: [Name] | Phone: [Phone] | ZIP: [ZIP] | Emergency: [Y/N] | Insurance: [Y/N] | Service: [Service] | Score: [Score] | Link: [HubSpot Link]

EMAIL:
Subject: New Lead - [Service] - [City] | Score: [Score]
MetroGuard has a new [Service] lead in [City].
Name: [Name] | Phone: [Phone] | ZIP: [ZIP]
Emergency: [Y/N] | Insurance: [Y/N]
Lead Score: [Score]
[View in HubSpot →]
```

### Step 5.4: Set Up Email Nurture Sequences
Create 3 sequences in HubSpot:

**Hot Lead Sequence (3 emails over 3 days):**
- Day 0: "Thanks [Name]! We're on it."
- Day 1: "What happens next - our process"
- Day 3: "Special offer: Free inspection this week"

**Warm Lead Sequence (4 emails over 7 days):**
- Day 0: "Thanks for contacting MetroGuard"
- Day 2: "5 signs your [Service] needs attention"
- Day 5: "Did you know? [Local tip]"
- Day 7: "Limited time offer"

**Cold Lead Sequence (weekly for 30 days):**
- Week 1: "5 basement prep tips for Metro Detroit"
- Week 2: "Seasonal flooding guide"
- Week 3: "Insurance claim tips"
- Week 4: "Final offer - Free consultation"

---

## 6. Testing Checklist

After setup, verify each component:

### Phone / Twilio:
- [ ] Call the main line from a mobile phone
- [ ] Verify greeting plays after 30 seconds
- [ ] Verify SMS is received after hanging up
- [ ] Verify call forwards to operator

### Form / Zapier:
- [ ] Submit test form entry
- [ ] Verify webhook fires in Zapier
- [ ] Verify contact appears in HubSpot
- [ ] Verify lead scoring calculates correctly

### Chatbot / Landbot:
- [ ] Visit website, click chat widget
- [ ] Complete full qualification flow
- [ ] Verify data appears in HubSpot
- [ ] Verify emergency routing triggers SMS

### Make.com:
- [ ] Submit test lead with score > 100
- [ ] Verify contractor receives SMS within 2 minutes
- [ ] Submit warm lead
- [ ] Verify email sequence starts

---

## 7. Monthly Maintenance

| Task | Frequency | Notes |
| :--- | :--- | :--- |
| Review lead scores | Weekly | Adjust scoring weights based on conversion data |
| Check contractor response times | Weekly | Alert if > 5 min response |
| Clean HubSpot pipeline | Monthly | Archive closed-lost leads |
| Review Twilio usage | Monthly | Adjust calling patterns if costs too high |
| Update chatbot | Monthly | Add new responses based on common questions |
| Check for spam leads | Weekly | Block obviously fake submissions |

---

## 8. Quick Start Summary (Day 1-3)

### Day 1:
- [ ] Create HubSpot account + pipeline + lead scoring properties
- [ ] Create Tally form and test webhook

### Day 2:
- [ ] Create Twilio account + purchase Detroit number
- [ ] Set up missed-call Studio flow
- [ ] Create Zapier integration: Tally → HubSpot

### Day 3:
- [ ] Create Landbot account + build chatbot
- [ ] Create Make.com account + build lead routing
- [ ] Test full flow end-to-end

---

## Status

**Current State:** This document serves as the **setup playbook**. Actual account creation and configuration needs to be performed by an operator with access to the respective platforms.

**Next Step:** Hand off to operator with this guide to complete the actual setup within 3-5 business days.