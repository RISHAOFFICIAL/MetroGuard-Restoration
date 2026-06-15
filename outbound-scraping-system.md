# MetroGuard Outbound Lead Discovery System — FREE STACK
## Side 2: Scraping + Qualification + Manual Outreach

**STACK:** Reddit API (free) + Craigslist RSS (free) + Google Alerts (free) + Make.com (free) + HubSpot (free)
**COST:** $0/month
**RULE:** No auto-DM. No auto-outreach. All scraped leads go to manual review in HubSpot first.

---

## Overview

This system finds Metro Detroit homeowners talking about water damage, flooding, and waterproofing online — then qualifies and routes them to HubSpot for manual outreach. It runs on free tools and a rules-based scoring engine (no AI needed).

**Why no auto-outreach?** Reddit, Craigslist, and Google Alerts posts are public but outreach must be manual to stay compliant with platform terms and to maintain quality control.

---

## 1. Sources — What We Monitor

### 1.1 Reddit API (Free, No Auth Required)

**How it works:** Public Reddit posts are accessible via JSON URL — no API key needed.

**Monitored Subreddits:**
| Subreddit | URL |
| :--- | :--- |
| r/Detroit | reddit.com/r Detroit.json |
| r/homeowners | reddit.com/r/homeowners.json |
| r/HomeImprovement | reddit.com/r/HomeImprovement.json |
| r/DIY | reddit.com/r/DIY.json |

**Make.com RSS Module URL format:**
```
https://www.reddit.com/r/{subreddit}/hot.json?limit=25
```

**Keywords to search in post titles and text:**
```
water damage, flooded basement, burst pipe, waterproofing, 
mold, sump pump, foundation leak, water heater, sewage backup,
pipe burst, ceiling leak, flooding, moisture, water intrusion,
basement flood, drain backup, pipe leak, water main break
```

**Make.com Module: HTTP — Get Request**
- Method: GET
- URL: `https://www.reddit.com/r/detroit/hot.json?limit=25`
- Headers: none required (public access)
- Run schedule: Every 3 hours

**Extract from JSON response:**
- `data[i].children[j].data.title` → Post title
- `data[i].children[j].data.selftext` → Post body
- `data[i].children[j].data.author` → Username
- `data[i].children[j].data.url` → Post URL
- `data[i].children[j].data.created_utc` → Timestamp

### 1.2 Craigslist RSS Feeds (Free)

**Metro Detroit Area Feed URLs:**
| City | URL |
| :--- | :--- |
| Detroit | `https://detroit.craigslist.org/search/sss?query=water+damage&format=rss` |
| Dearborn | `https://detroit.craigslist.org/search/sss?query=basement+waterproofing&format=rss` |
| All Metro Detroit | `https://detroit.craigslist.org/search/sss?query=flooding+OR+water+damage+OR+basement&format=rss` |

**Make.com Module: RSS — Watch RSS Feed**
- RSS URL: `https://detroit.craigslist.org/search/sss?query=water+damage&format=rss`
- Run schedule: Every 6 hours

**Keywords for Craigslist:**
```
water damage, flooded basement, waterproofing, burst pipe,
mold, sewage, sump pump, foundation repair, water heater,
pipe leak, flood, moisture, water intrusion
```

**Extract from RSS items:**
- `item.title` → Listing title
- `item.description` → Listing text
- `item.link` → Listing URL
- `item.pubDate` → Posted date

### 1.3 Google Alerts (Free)

**Set up free Google Alerts at:** [google.com/alerts](https://google.com/alerts)

**Alert queries (one per alert):**
```
"water damage Detroit"
"basement flooding"
"waterproofing contractor Detroit"
"burst pipe Detroit"
"mold remediation"
"sump pump installation"
"flooded basement Michigan"
"pipe leak Detroit"
```

**Delivery:** All results go to a dedicated Gmail inbox (create one specifically for this: `metroguardleads@gmail.com`)

**Make.com Module: Email — Watch Emails**
- Connection: `metroguardleads@gmail.com` (IMAP)
- Filter: From `googlealerts-noreply@google.com`
- Folder: INBOX

**Extract from Google Alert email:**
- Subject line → Alert query
- Body → Lead text snippet
- Body → Link to original source

---

## 2. Qualification Engine — Rules-Based Scoring

**No AI needed.** Simple keyword scoring in Make.com:

### Keyword Weights

| Category | Keywords | Points |
| :--- | :--- | :--- |
| **Emergency** | flooded, flood, burst pipe, broken pipe, sewage backup, no water, gas leak, water main break | +100 |
| **Urgency** | help, urgent, asap, immediately, emergency, can't wait, not functioning, broken | +50 |
| **Service — Water Damage** | water damage, extraction, drying, structural drying, wet carpet, standing water | +30 |
| **Service — Basement** | basement, downstairs, cellar, below grade, sump pump failure | +30 |
| **Service — Waterproofing** | waterproofing, french drain, interior drain, exterior coating, foundation seal | +30 |
| **Service — Mold** | mold, mildew, black mold, smell mold, musty odor | +30 |
| **Service — Foundation** | foundation crack, foundation repair, settled, shifting, beam | +30 |
| **Insurance** | insurance, filed claim, my insurance, coverage, adjuster | +20 |
| **Geo — Metro Detroit** | detroit, dearborn, troy, birmingham, royal oak, grosse pointe, warren, sterling heights, livonia, canton, west bloomfield, oak park, southfield, romulus, wyandotte | +20 |

### Score Tiers

| Score | Tier | Action |
| :--- | :--- | :--- |
| 150+ | **CRITICAL** | Create HubSpot contact, tag "Prospect — Needs Outreach (Critical)" |
| 80–149 | **QUALIFIED** | Create HubSpot contact, tag "Prospect — Needs Outreach" |
| 50–79 | **WATCH** | Create HubSpot contact, tag "Prospect — Watch List" (check if local) |
| < 50 | **DISCARD** | Do not create contact. Discard automatically. |

### Metro Detroit ZIP Codes (for geo validation)

**Primary Metro Detroit ZIPs:**
```
Detroit: 48201, 48202, 48203, 48204, 48205, 48206, 48207, 48208, 48209, 48210,
        48211, 48212, 48213, 48214, 48215, 48216, 48217, 48219, 48221, 48223,
        48224, 48225, 48226, 48227, 48228, 48234, 48235, 48238, 48239

Metro Counties: 48009, 48015, 48025, 48030, 48034, 48037, 48038, 48042, 48044,
                48047, 48054, 48060, 48062, 48066, 48067, 48068, 48071, 48073,
                48076, 48081, 48083, 48084, 48085, 48088, 48089, 48090, 48091,
                48092, 48093, 48094, 48096, 48098, 48099, 48101, 48111, 48125,
                48127, 48128, 48146, 48150, 48152, 48154, 48167, 48174, 48180,
                48184, 48185, 48186, 48187, 48188
```

*If ZIP is not in list → Geo score = 0, reduce overall score accordingly.*

---

## 3. Make.com Scenario — Full Flow

### TRIGGER: Reddit RSS (runs every 3 hours) OR Craigslist RSS (runs every 6 hours) OR Google Alert email

```
[SOURCE TRIGGER]
  Reddit JSON API / Craigslist RSS / Gmail Alert Email
          │
          ▼
[PARSE DATA]
  Extract: title, body/text, author/username, URL, timestamp
          │
          ▼
[KEYWORD SCORING — Router]
  Search text for emergency keywords → +100 if hit
  Search text for urgency keywords → +50 if hit
  Search text for service keywords → +30 per category
  Search text for insurance → +20 if hit
  Search text for geo keywords → +20 if hit
          │
          ▼
[SCORE FILTER]
  IF score >= 150 → CRITICAL
  IF score >= 80 → QUALIFIED
  IF score >= 50 → WATCH
  IF score < 50 → DISCARD (end scenario)
          │
          ▼
[HUBSPOT — Create Contact]
  Properties:
    firstname: [author/username]
    lead_status: "Prospect — Needs Outreach"
    lead_source: [Reddit/Craigslist/Google Alerts]
    notes: [original post text]
    websiteurl: [URL]
    city: [extracted from geo keywords]
          │
          ▼
[HUBSPOT — Add Note]
  Note: "Scraped prospect from [platform]. Score: [score]. [original post text excerpt]. [link]"
          │
          ▼
[EMAIL TO OWNER]
  To: [your email]
  Subject: "New Metro Detroit Lead: [author/username] on [platform]"
  Body: "New scraped prospect qualifies for outreach. Platform: [Reddit/Craigslist/Google]. Score: [score]. Summary: [1-2 sentence excerpt]. Source: [URL]. Review in HubSpot and decide: DM manually or pass."
```

### Make.com Module Order

1. **RSS — Watch RSS Feed** (Reddit) OR **HTTP — Get Request** (Reddit JSON) OR **RSS — Watch RSS Feed** (Craigslist)
2. **Text Parser — Parse JSON** (for Reddit)
3. **Text Parser — Search with Regex** (keyword matching)
4. **Router — Multiple paths** (by score threshold)
5. **HubSpot — Create a Contact** (for qualified scores)
6. **HubSpot — Create Note** (with source URL)
7. **Email — Send me an email** (notification)

---

## 4. HubSpot Setup

### Custom Properties for Scraped Leads

Create these properties in HubSpot → Settings → Properties → Contact:

| Property | Type | Notes |
| :--- | :--- | :--- |
| `lead_source` | Enumeration | Reddit, Craigslist, Google Alerts, Scraper |
| `scraper_score` | Number | 0–200 |
| `scraper_platform` | Single-line text | r/detroit, detroit.craigslist.org, google |
| `original_post_url` | URL | Link to the scraped post |
| `original_post_text` | Multi-line text | Full post text |
| `lead_status` | Enumeration | Prospect — Needs Outreach, Prospect — Watch List |

### Pipeline

All scraped leads go into the **standard MetroGuard pipeline**:
- Stage: New Lead → Qualified (if high score) or kept as New Lead (for review)
- Status: "Prospect — Needs Outreach"
- Owner: You (manual review)

### Views for Scraped Leads

Create a HubSpot saved view: **"Scraped Prospects — Needs Outreach"**
- Filter: `lead_source` = "Scraper" AND `lead_status` = "Prospect — Needs Outreach"
- Sort by: `scraper_score` descending

---

## 5. Manual Outreach Rules

**⚠️ CRITICAL: No auto-DM. No auto-outreach.**

All scraped leads require manual review and manual outreach:

**Owner workflow:**
1. Check HubSpot saved view every morning
2. Review each prospect: read post, check profile, assess legitimacy
3. If legitimate lead → DM/email manually via platform
4. If not → mark as "No Response" in HubSpot

**Why this rule exists:**
- Reddit and Craigslist terms prohibit automated outreach
- Automated DMs get accounts banned
- Manual outreach is higher quality and higher conversion anyway
- Protects your account and reputation

**Outreach scripts:**

*Reddit DM:*
```
Hi [username], I saw your post about [issue]. I'm part of MetroGuard Restoration — we help Metro Detroit homeowners with water damage emergencies 24/7. If you still need help or want a free assessment, happy to assist. Happy to share our 60-min response guarantee.
```

*Craigslist reply:*
```
Hi — saw your posting about [issue]. We're MetroGuard Restoration and we service the [City] area. If it's still relevant, we offer free inspections and can typically be on-site within an hour for emergencies. Interested?
```

*Google Alert follow-up:*
```
Hi [name], I saw the alert about [issue]. We're a local Metro Detroit water damage company. If you need immediate help or want a second opinion on pricing, we're happy to provide a free phone consultation. Let me know.
```

---

## 6. What to Watch For (Red Flags)

When reviewing scraped leads, these are NOT real leads — discard:
- Contests, giveaways, or promotional posts
- Contractors advertising their own services
- Spam links or suspicious URLs
- People asking "who does X" without an active problem
- Out-of-area requests (not Metro Detroit)

**Good indicators (real leads):**
- Person describing an active problem ("my basement flooded last night")
- Urgency language ("I need help now," "the water is spreading")
- Location is a Metro Detroit ZIP
- Insurance mention ("I have State Farm and need to file")
- Specific damage description

---

## 7. Implementation Checklist

### Day 1:
- [ ] Create `metroguardleads@gmail.com` for Google Alerts
- [ ] Set up 7 Google Alerts queries (list above)
- [ ] Create HubSpot custom properties for scraped leads
- [ ] Create saved view "Scraped Prospects" in HubSpot

### Day 2:
- [ ] Build Make.com scenario: Reddit → Parse → Score → HubSpot → Email
- [ ] Test with 3 sample Reddit JSON calls
- [ ] Test with 3 Craigslist RSS results

### Day 3:
- [ ] Build Make.com scenario: Craigslist RSS → Parse → Score → HubSpot
- [ ] Test full end-to-end
- [ ] Set up email forwarding from `metroguardleads@gmail.com` to Make.com Gmail integration

### Day 4:
- [ ] Run all 3 scrapers live
- [ ] Review first scraped prospects in HubSpot
- [ ] Send first manual outreach DMs

---

## 8. Monthly Cost Summary

| Tool | Cost | Purpose |
| :--- | :--- | :--- |
| Reddit API | $0 | Public JSON access — no key needed |
| Craigslist RSS | $0 | Public RSS feeds |
| Google Alerts | $0 | Free with any Google account |
| Make.com | $0 | Free tier (1,000 ops/mo — enough for startup) |
| HubSpot Free CRM | $0 | Contact management and lead pipeline |
| **Total** | **$0/mo** | Fully free outbound lead discovery |

---

## Status

**Built:** Complete setup guide, Make.com scenario specs, keyword scoring rules, Reddit/Craigslist URLs, HubSpot configuration, outreach scripts.

**Owner action required:**
- Create `metroguardleads@gmail.com`
- Set up Google Alerts (7 queries)
- Configure HubSpot custom properties
- Build Make.com scenarios (step-by-step above)

**Manual outreach is the constraint.** This system discovers leads for free — you close them manually. That's the tradeoff of staying on the free stack.
