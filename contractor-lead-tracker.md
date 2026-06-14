# MetroGuard Contractor Lead Tracker
## Google Sheets Template Reference

**Create a new Google Sheet named "MetroGuard Contractor Leads"**
Use these exact column headers — Make.com and HubSpot will sync to these:

| Column | Header | Example |
| :--- | :--- | :--- |
| A | Timestamp | 2024-06-14 |
| B | Business Name | ABC Water Damage LLC |
| C | Contact Name | John Smith |
| D | Email | john@abcwater.com |
| E | Phone | (313) 555-1234 |
| F | City / Territory | Dearborn |
| G | Service Type | Water Damage Restoration |
| H | Monthly Capacity | 6-15 jobs/month |
| I | IICRC Certified | Yes |
| J | Status | Lead / Contacted / Interested / Signed Up / No Response |
| K | Notes | Called twice, left voicemail |
| L | Email Sequence Step | Email 1 / Email 2 / Email 3 / Email 4 / Email 5 / Done |
| M | Last Contact Date | 2024-06-10 |
| N | Contract Signed | Yes / No |
| O | Retainer Tier | PPL / Ready-Response |
| P | Monthly Retainer | $300 |
| Q | Lead Count (Month) | 7 |
| R | Revenue (Month) | $1,050 |

**To create the Google Sheet:**
1. Go to sheets.new
2. Copy column headers above into row 1
3. Freeze row 1 (View → Freeze → 1 row)
4. Set up conditional formatting: Status = "Signed Up" → green row
5. Share with your team

**Make.com Integration:**
In Make.com → Google Sheets module → use "Search Rows" matching on Status = "Lead" to trigger email sequence.

---

## Quick Dashboard View (optional summary sheet)

Create a second sheet tab called "Dashboard" with:

| Metric | Count/Value |
| :--- | :--- |
| Total Leads | =COUNTA(A:A)-1 |
| Signed Up | =COUNTIF(J:J,"Signed Up") |
| Interested | =COUNTIF(J:J,"Interested") |
| No Response | =COUNTIF(J:J,"No Response") |
| Ready-Response Partners | =COUNTIF(O:O,"Ready-Response") |
| Total Monthly Revenue | =SUM(P:P) |
| Avg Leads per Partner | =IF(COUNTIF(J:J,"Signed Up")>0, total_leads/COUNTIF(J:J,"Signed Up"), 0) |