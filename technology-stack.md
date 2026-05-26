# Technology Stack — Low-Cost, High-Automation Lead Gen Stack
## MetroGuard Restoration | Tech Architecture

---

## 1. Stack Philosophy

**Guiding Principles:**
- Minimize monthly overhead (< $300/mo start)
- Maximize automation so the system runs itself
- Use no-code/low-code tools for fast iteration
- Scale via adding city/niche sites, not engineering

**Core Stack:** 80% of lead gen sites in this range run on: a page builder + CRM + call tracking + AI chatbot + zapier/make.

---

## 2. Recommended Technology Stack

### 2.1 Website Builder / CMS

| Option | Cost | Best For |
| :--- | :--- | :--- |
| **Carrd.co** | $19/mo | Fast, beautiful one-page landing site. Perfect for MVP/Emergency Dispatch page. |
| **WordPress + Elementor** | $30-50/mo | Full multi-page site with blog. More SEO power. |
| **Webflow** | $14/mo (CMS plan) | Premium design, more complex SEO. Higher learning curve. |

**Recommendation for MetroGuard:**
- **Phase 1:** Carrd for the Emergency Dispatch page + a fast WordPress install for the main site.
- **Phase 2:** Full WordPress site with city pages and blog.
- **Domain:** metroguardmi.com (or metroguardrestoration.com — $12/yr via Namecheap)

**Why Carrd first?** Speed to market. A beautiful emergency landing page can be live in 2 hours. This is the #1 priority for the 30-day sprint.

### 2.2 Call Tracking & Phone System

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Twilio** | $1/min + $0.0085/sms | Full phone control, missed-call texts,IVR routing |
| **CallRail** | $49/mo starter | Call tracking, recording, Google Ads integration |
| **Google Voice** | $0 | Single tracking number for startup phase |

**Recommendation:**
- **Startup:** Google Voice (free) → upgrade to Twilio when calls start flowing.
- **Growth:** Twilio + CallRail combo for full call attribution by source.

**Key Automation:** Missed calls trigger an SMS auto-responder: "Thanks for calling MetroGuard! We'll have a specialist call you back within 5 minutes. For immediate help, text BACK to this number."

### 2.3 CRM & Lead Management

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **HubSpot Free CRM** | $0 | Contact management, lead pipelines, email tracking |
| **Airtable** | $10/mo | Custom lead tracking, more flexible than CRM for small ops |
| **Google Sheets** | $0 | Simple lead log, works with Zapier |
| **不下** | $0 | Great for single-user simple tracking |

**Recommendation:**
- **Phase 1:** HubSpot Free (unlimited contacts, pipeline view, email tracking).
- **Phase 2:** Airtable for more complex automation and reporting.
- **Why HubSpot:** It's free, has great lead scoring, and contractors are familiar with it.

### 2.4 AI Chatbot / Virtual Receptionist

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Landbot.io** | $30/mo | No-code chatbot for website,Qualifies leads 24/7 |
| **Voiceflow** | $0-80/mo | Voice + text AI agent, More powerful for phone |
| **Chatfuel** | $0-15/mo | Facebook/Website bot |
| **Botpress** | $0 | Open-source, self-hosted AI chatbot |

**Recommendation:**
- **Phase 1:** Landbot (easiest setup, drag-and-drop builder) on the quote request page.
- **Phase 2:** Voiceflow for an AI receptionist that answers the phone and routes leads.
- **Key Feature:** AI chatbot should ask: "What's your address?", "Is this an emergency?", "Are you filing an insurance claim?" to pre-qualify leads before routing to contractors.

### 2.5 Form Automation

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Tally Forms** | $0/mo | Free, beautiful, captures leads to email |
| **Jotform** | $0/mo | More integrations, HIPAA-friendly |
| **Typeform** | $0/mo | Conversational forms, Higher conversion |
| **Native HTML Forms** | $0 | Free, connected to Zapier via webhooks |

**Recommendation:** Tally (free tier is generous) + Zapier webhook to push form data into HubSpot CRM.

### 2.6 SMS Automation

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Twilio** | $0.0085/sms | Outbound SMS, automation |
| **Gush** | $30/mo | Simple SMS for small teams |
| **SimpleTexting** | $25/mo | More CRM-friendly |

**Recommendation:** Twilio + Make.com (Zapier alternative — same price, more flexible).

### 2.7 Email Automation

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Mailchimp** | $0/mo | Free up to 500 contacts, Basic automations |
| **ConvertKit** | $0/mo | Better for blogging, creators |
| **HubSpot Email** | $0 | Included with free CRM |

**Recommendation:** HubSpot Free (already in stack) — it handles email follow-up sequences and is natively connected to the CRM.

### 2.8 Marketing & SEO Tools

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Google Search Console** | $0 | SEO performance monitoring |
| **Google Analytics 4** | $0 | Traffic and conversion tracking |
| **Ubersuggest** | $12/mo | Keyword research, SEO auditing |
| **Brightlocal** | $48/mo | Local SEO citations and rank tracking |
| **Screaming Frog** | $0 (crawl 500 URLs) | Technical SEO audit |

**Recommendation:**
- Start with Google tools (free). Add Ubersuggest when you need keyword research. Add Brightlocal when you're ready to scale local SEO across multiple cities.

### 2.9 Payment / Billing

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| **Stripe** | 2.9% + $0.30 | Accept payments from contractors |
| **PayPal** | 2.9% + $0.30 | Simpler, less professional |
| **Wave** | $0 | Free invoicing for small businesses |

**Recommendation:** Wave for invoicing (free, professional). Stripe when you need recurring payments.

---

## 3. Full Monthly Cost Estimate

| Category | Tool | Monthly Cost |
| :--- | :--- | :--- |
| Website | Carrd + WordPress | $19 + $10 = $29 |
| Domain | Namecheap | $1/mo |
| Call Tracking | Google Voice (start) / Twilio (growth) | $0-20 |
| CRM | HubSpot Free | $0 |
| AI Chatbot | Landbot | $30 |
| Forms | Tally | $0 |
| SMS/Automation | Twilio + Make.com | $30 + $10 = $40 |
| SEO Tools | Ubersuggest | $12 |
| **Total** | | **$100-150/mo** |

This is extremely lean. The main costs come online once you have revenue to justify them.

---

## 4. Implementation Priority Order

### Immediate (Day 1-7):
1. Register domain (metroguardmi.com)
2. Set up Carrd emergency page with click-to-call
3. Get a Google Voice tracking number
4. Set up HubSpot Free CRM

### Week 2:
5. Set up Tally form + Zapier webhook
6. Set up Twilio for missed-call SMS
7. Configure Landbot on quote page

### Week 3-4:
8. Add Ubersuggest for keyword tracking
9. Set up Google Business Profile
10. Configure Google Analytics 4

---

## 5. Scaling Playbook

When monthly revenue hits $5K+, upgrade:
- WordPress (from Carrd) → Better SEO and more pages
- CallRail ($49/mo) → Better attribution than Twilio alone
- Brightlocal ($48/mo) → Multi-city citation management
- HubSpot Starter ($50/mo) → More CRM power

The goal is to keep fixed costs under $300/mo while revenue scales to $10K-20K/mo.