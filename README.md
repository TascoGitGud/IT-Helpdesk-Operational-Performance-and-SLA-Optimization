![Power BI](https://img.shields.io/badge/Tool-Power_BI-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)

---

<p align="center">
  <img src="Images/banner.jpg" width="100%">
</p>

# 📊 IT Helpdesk Operational Performance & SLA Optimization | Power BI

_An interactive dashboard built to help IT managers track support tickets, find bottlenecks in the system, and reduce missed deadlines (SLA breaches)._

- 🎯 **Main Goal:** How can we reduce missed deadlines and speed up ticket resolution across different support teams and countries?
- 🏬 **Area:** IT Service Management (ITSM) / Business Operations
- 🛠️ **Tools:** Power BI

👤 Author: Bach Minh Nam

---

## 📑 Table of Contents
1. [📌 Background & Goals](#-background--goals)
2. [📂 About the Data](#-about-the-data)
3. [🧠 How We Built It](#-how-we-built-it)
4. [📊 Key Findings](#-key-findings--visualizations)
5. [🔎 Final Conclusion & What To Do](#-final-conclusion--recommendations)

---

## 📌 Background & Goals

### What Problem Are We Solving?

The IT Manager needs a dashboard to answer 3 main questions:

✔️ **Current Situation:** How many tickets arrive each month? How fast are we fixing them? What's the trend?

✔️ **Where Are We Failing:** Which support teams are missing deadlines? Where do tickets get stuck the longest?

✔️ **What Takes Longest:** Do certain types of tickets take longer to resolve? Which categories cause the most delays?

The goal is to have **one central dashboard** that shows all this information clearly so leadership can make smart decisions about hiring, training, and process improvements.

### 👤 Who Will Use This?

✔️ **IT Managers & Support Team Leaders** - Check the dashboard daily to monitor team performance, spot problems quickly, and track how fast tickets get resolved.

✔️ **Executives & Leadership** - Review weekly for big-picture view of service quality and operational health. Need clear proof for budget decisions.

✔️ **Regional & Queue Leaders** - See which teams need more staff or training. Understand how their region/team compares to others.

---

## 📂 About the Data

### Data Source & Size
* **Where it comes from:** IT Support Ticket System
* **File type:** Excel spreadsheet (.xlsx)
* **Amount:** 11,923 support tickets tracked over 20 months
* **Basic unit:** Each row = 1 ticket

### What Information We Have About Each Ticket?

| Column Name | What It Means |
| :--- | :--- |
| **Ticket ID** | Unique number for each ticket |
| **Date** | When the ticket was created |
| **Resolution Date** | The deadline when it should be fixed |
| **Subject** | What the ticket is about |
| **Body** | Detailed description of the issue |
| **Answer** | What the support team responded with |
| **Type** | Category: Request, Problem, Incident, or Change |
| **Queue** | Which team handles it (Tech Support, Customer Service, HR, etc.) |
| **Priority** | Level: Low, Medium, or High |
| **Primary Tag** | Main category label |
| **Secondary Tag** | Additional category label for context |
| **Category Tag** | General category label |
| **Technical Tag** | Specific technical category |
| **Status Tag** | Current status or nature of the issue |
| **Resolution Tag** | How it was resolved |
| **Documentation Tag** | Reference to documentation |
| **Additional Tag** | Any other relevant labels |
| **Country** | Which country the request came from |
| **Latitude** | Location coordinates |
| **Longitude** | Location coordinates |

---

## ⚙️ Data Organization & Setup

### The Problem With Raw Data
Originally, all the information was mixed together in one big table. Same information repeated many times, made filtering slow, made reporting difficult.

### What We Did

### A. Separated Information Into Smaller, Organized Tables
Instead of one messy table, we created:
- **Team Table:** Lists all support queues (Customer Service, Technical Support, HR, etc.)
- **Priority Table:** Lists priority levels (Low, Medium, High)
- **Type Table:** Lists ticket types (Request, Problem, Incident, Change)
- **Country Table:** Lists all countries
- **Tags Table:** Lists all the issue categories/labels
- **Date Table:** Calendar for time-based analysis
- **Main Ticket Table:** Core ticket information

This way, we don't repeat "Customer Service" 500 times in the data - we just reference it once.

### B. Handled Multiple Tags Per Ticket Properly
The raw data had 8 different tag columns. One ticket could have multiple tags. Instead of keeping them spread across columns, we organized them in a special bridge table:
- Unpivoted (reorganized) all 8 tag columns into 2 neat columns: `Tag Name` and `Tag Type`
- Created a **Dim_Tag** table with unique list of all tags
- Created a **Bridge_Ticket_Tag** table that links tickets to their tags
- This way tickets keep their information clean, and we can still analyze by any tag combination

### C. Set Up Time Intelligence for Trends
- Created a **Date Table** using formulas so we can easily calculate trends month-to-month and year-to-year
- Connected dates to the main ticket table for fast analysis

---

## 📊 Final Data Structure & How Tables Connect

### Tables In Our System
**8 total tables** organized like this:

**1 Main Table (holds ticket information):**
* **Fact_Ticket** - All the core ticket details and numbers

**1 Bridge Table (connects tickets to multiple tags):**
* **Bridge_Ticket_Tag** - Links each ticket to all its tags

**6 Lookup Tables (support information):**
* **Dim_Queue** - List of all support teams
* **Dim_Priority** - List of priority levels
* **Dim_Type** - List of ticket types
* **Dim_Country** - List of countries with location info
* **Dim_Tag** - List of all issue tags/categories
* **Dim_Date** - Calendar table for time analysis

### Core Tables Explained

#### Fact_Ticket (Main Ticket Information)
| Column Name | What It Contains |
| :--- | :--- |
| Ticket ID | **Unique ID.** The main identifier for each ticket. |
| Subject | The headline of the ticket. |
| Body | Full description of the problem. |
| Answer | Response from the support team. |
| Date | When the ticket was created. |
| Resolution Date | When it was closed. |
| Queue ID, Priority ID, Type ID, Country ID | Numbers that link to the lookup tables |

#### Dim_Tag (All Issue Categories)
| Column Name | What It Contains |
| :--- | :--- |
| Tag ID | **Unique ID.** Number for each tag. |
| Tag Name | The actual tag text (like "Technical", "Billing", "Security"). |
| Tag Type | What type of tag it is (Primary, Secondary, Technical, Status, etc.). |

#### Bridge_Ticket_Tag (Linking Tickets to Tags)
| Column Name | What It Contains |
| :--- | :--- |
| Ticket ID | Links back to a ticket. |
| Tag ID | Links to a tag. |

### How Tables Connect to Each Other
* **Date Table → Ticket Table:** 1 date connects to many tickets (one-to-many)
* **Queue/Priority/Type/Country → Ticket Table:** 1 queue connects to many tickets (one-to-many)
* **Ticket Table → Bridge Table:** 1 ticket connects to many tags (one-to-many)
* **Tag Table → Bridge Table:** 1 tag connects to many tickets (one-to-many)

<p align="center">
  <img src="Images/data_model.png" width="80%">
</p>

## 🧠 How We Planned This Dashboard

We followed a simple 3-step thinking process to understand what stakeholders need and what to show them.

### 1️⃣ Understanding the Users

| Question | Answer |
|---|---|
| **Who looks at this dashboard?** | IT Managers, Service Desk Leads, and Executives. |
| **What problem does it solve?** | Shows where tickets are getting stuck, why deadlines are missed, and which teams need help. |
| **When is it used?** | IT Managers check it daily. Executives review it weekly in meetings. |
| **Why do they need it?** | Leadership complains about missed deadlines but doesn't know why. Teams don't know where to improve. Need clear data proof to get budget for hiring or training. |
| **How will they use it to make decisions?** | Compare team performance, see which regions are slow, understand which ticket types take longest, decide how to allocate staff. |
| **What problems do they face now?** | • Reports scattered across different systems.<br>• No clear view of which teams are struggling.<br>• High missed deadlines but no proof of why.<br>• Hard to show executive why more resources are needed. |
| **What will help them?** | • Everything in one dashboard.<br>• Clear data showing exact bottlenecks.<br>• Clear evidence for business decisions.<br>• Quick way to spot and fix problems. |
| **Key Questions They Need Answered** | • How many tickets total? How fast are we fixing them?<br>• Which team misses most deadlines?<br>• Which type of ticket takes longest?<br>• Which country is slowest?<br>• Is ticket volume growing or shrinking? |

### 2️⃣ Main Dashboard Perspectives (What to Show)

| Perspective | What It Shows | Why It Matters |
|---|---|---|
| **Overall Health** | Total tickets, average speed, total breach rate, monthly trends | Shows the big picture first before looking at details. |
| **Ticket Types & Categories** | How many of each type? How long does each type take? Which categories are fastest/slowest? | Shows which work is causing problems and where to focus. |
| **Teams & Regions** | How fast is each team? How fast is each country? Who's doing well, who's struggling? | Shows exactly where to hire more staff or provide training. |

**Key Metrics We Track:**

| Metric 1 | Metric 2 |
|---|---|
| **% Missed Deadlines (SLA Breach %)** | **Average Time to Fix (MTTR)** |
| How to calculate: Count tickets resolved late ÷ total tickets × 100 | How to calculate: Add up all resolution times ÷ number of tickets |
| Goal: Get below 50% and keep dropping month by month. | Goal: Keep under 3 days, ideally trend toward 2.5 days. |
| Why it matters: Shows how often we fail customers. High numbers mean customers are unhappy and lose trust. | Why it matters: Shows how fast the team actually works. If this is too high, everything else fails. |

### 3️⃣ How We Organized the Dashboard Pages

| | **Page 1: Overview** | **Page 2: Ticket Types** | **Page 3: Resolution & Locations** |
|---|---|---|---|
| **Top Section (Key Numbers)** | Total tickets, average speed, breach %. | Total by type, top categories. | Daily average, average resolution time. |
| **Middle Section (1 dimension breakdown)** | Monthly trend line, breakdown by priority. | Top 5 categories, breach % by day of week. | Geographic map, team comparison. |
| **Bottom Section (2 dimension breakdown)** | Growth rates across categories and time. | Comparison: category × priority. | Calendar heatmap (day × month), Team Matrix (speed vs breach %), Type × Country performance. |

---

## ⚒️ Main Process

1️⃣ **Load Data** - Connected Power BI to the ticket file, checked all data types, organized in Power Query.

2️⃣ **Build Data Model** - Took flat structure and organized it into separate clean tables, unpivoted the tags, created bridge table, built Star Schema.

3️⃣ **Create Formulas** - Wrote formulas to calculate total tickets, average resolution time, breach rate, month-over-month changes, and team performance.

4️⃣ **Build Dashboard** - Created 5 interactive pages with filters, hover tooltips, drill-down details, and navigation buttons.

---

## 📊 Key Insights & Findings

### 🔍 Dashboard Features

> **Advanced Features We Used**
>
> - 🔖 **Filters** - Hidden filter panel at bottom-left. Click to show/hide. Filters include: Date range (1/1/2024 – 6/4/2025), Month, Queue, Priority, Country. These filters work on all pages at once.
>
> <p align="center"><img src="Images/Bookmarks.png" width="40%"></p>
>
> - 🔍 **Drill-Down Details** - Click any data point to see individual ticket details. Shows: Ticket ID, Priority, Queue, Tag, Status, Created Date, Resolution Date, Days to Resolve.
>
> <p align="center"><img src="Images/Drill_Though.png" width="100%"></p>
>
> - 💬 **Hover for More Info** - Hover your mouse over data points to see more details without cluttering the page.
>
> <p align="center"><img src="Images/tooltip1.png" width="48%"> <img src="Images/tooltip2.png" width="48%"></p>
>
> - 🔙 **Back Button** - On the detail page, click back arrow to return to the main page.

---

#### 1️⃣ Page 1 - Helpdesk Overview

<p align="center">
  <img src="Images/Overview.png" width="100%">
</p>

**What The Data Shows:**

The helpdesk processed **11,923 total tickets** (up 6.3% from last month) with an average resolution time of **2.82 days** - right at the deadline limit. The biggest concern: **61.66% of tickets missed their deadline** - almost 2 out of 3 tickets were resolved late. 

Monthly ticket volume averaged **701 tickets/month** with a low of 617 in January 2025 and high of 707 in May 2025. By priority: **Medium tickets are 41.53%** of work, **High is 38.34%**, and **Low is 20.13%**. Every ticket type is growing **5.8-6.7% each month**, showing consistent increase in work.

**Why This Matters:**
- A 61.66% missed deadline rate is a serious problem. Nearly 2 in 3 tickets are late - this is not acceptable.
- The 2.82 day average is exactly at the 3-day deadline. **Zero buffer** for any delays.
- Ticket volume growing 6% monthly means workload is increasing. If nothing changes, backlogs will get worse.
- So many "High" priority tickets (38%) suggests teams might be over-classifying urgency - not everything can be urgent.

**What To Do:**
- 🔴 **CRITICAL:** The 61.66% breach rate needs immediate investigation. Pick one team (worst performer), take 20 recent tickets, and trace where time is lost. Is it waiting for customer info? Unclear requirements? System problems? Staffing? Once you know the reason, you can fix it.
- 🟡 **Check the January dip:** Why did volume drop to 617 in January (lowest month)? Was it holiday break? System issue? Understanding this helps validate data quality.
- 🟡 **Plan for growth:** 6% monthly growth means 15-20% higher volume by end of year. Start hiring and planning now.
- 🟢 **Review priority classification:** Too many tickets marked "High" priority. Maybe create better guidelines so teams classify correctly.

---

#### 2️⃣ Page 2 - Ticket Types & Categories

<p align="center">
  <img src="Images/Ticket_Types.png" width="100%">
</p>

**What The Data Shows:**

**By Type:** **Incidents** are 38.93% of tickets, **Requests** are 29.34%, **Problems** are 20.95%, and **Changes** are 10.78%.

**By Category:** **Technical issues** are most common (3,100+ tickets), **Security** is second (2,000+ tickets). Both are growing faster than other categories (5.9-6.1% monthly).

**By Team Performance:**
- **Customer Service:** 81.87% missed deadline rate (1,859 tickets) ❌ WORST
- **Human Resources:** 88.78% missed rate (205 tickets) ❌ VERY BAD
- **General Inquiry:** 83.33% missed rate (168 tickets) ❌ BAD  
- **Technical Support:** 42.09% missed rate (3,412 tickets) ✅ BEST
- **Service Outages:** 30.09% missed rate ✅ BEST

**By Day of Week:** Missed rates are **60-63% every day**. **Sunday is worst at 63.06%**. No day is significantly better.

**Why This Matters:**
- **This is PROOF that the problem is NOT too much work - it's BAD PROCESS.** Technical Support handles 3,412 tickets but only misses 42% of deadlines. HR handles 205 but misses 89%. Same company, same rules, completely different results. If HR is bad at managing 205 tickets, it's a process problem, not workload.
- Customer Service is huge (1,859 tickets) AND doing terribly (81.87% miss rate). This is multiple problems combined.
- Sunday being worse suggests weekend staffing is too low.
- Technical and Security are growing faster - might need more specialists in these areas.

**What To Do:**
- 🔴 **Customer Service is your biggest target:** 1,859 tickets with 81.87% missed rate. Audit 20 recent tickets and understand: Are customer requirements unclear? Do they need multiple follow-ups? Is escalation path broken? Do they need more staff? Fix the root cause.
- 🔴 **Human Resources is worst of all:** Only 205 tickets but 88.78% miss rate. This is NOT a workload problem. Either they don't have right skills, process is broken, or priority is wrong. Interview the team.
- 🟡 **General Inquiry needs better definition:** "General" queues tend to be vague. What should go here? What should route elsewhere? Define it clearly so tickets get to right team faster.
- 🟢 **Copy Technical Support's approach:** They handle most tickets cleanly. Ask them: How do you prioritize? What tools help? How do you escalate? Document it and train other teams.
- 🟡 **Add weekend staff:** Even 1-2 more people on Sunday could reduce that 63% miss rate.
- 🟢 **Watch fast-growing categories:** Hire specialists for Technical and Security if volume keeps growing.

---

#### 3️⃣ Page 3 - Resolution Times

<p align="center">
  <img src="Images/Resolution_Times.png" width="100%">
</p>

**What The Data Shows:**

**Overall:** Average daily tickets = **22.88**, average resolution time = **2.82 days**, missed deadline rate = **61.66%**.

**By Ticket Type:**
- **Incidents:** 8.9 per day, **2.7 days to resolve** ✅ FASTEST - teams prioritize these
- **Requests:** 6.7 per day, **2.9 days to resolve** ⚠️ SLOWEST
- **Problems:** 4.6 per day, **2.75 days to resolve**
- **Changes:** 2.7 per day, **2.8 days to resolve**

**By Priority:**
- **High:** 2.83 days (reasonable)
- **Medium:** 2.81 days (reasonable)
- **Low:** Accounts for **44.44% of total time** 🚨 BIG PROBLEM

This means **low-priority tickets get ignored until they're about to miss deadline**, wasting a lot of team time.

**Timeline:** Heaviest volume **Sep-Dec 2024**. Lighter in early 2024 and early 2025.

**Why This Matters:**
- **Request tickets (2.9 days) are using almost all the buffer.** Even a tiny delay pushes them past the 3-day deadline. Why are they slow? Do they need too many approvals? Are requirements unclear? Are they routed to the wrong team?
- **Low-priority tickets at 44% of time is backwards.** They should be 15-20% of time. The pattern: ignore them, then panic when about to breach, then scramble to fix. This wastes time and stresses the team.
- **Incidents are handled well (2.7 days) even though they sound complex.** This means the incident process works. Apply the same logic to Requests.

**What To Do:**
- 🔴 **Request tickets need a process overhaul:** They're the slowest (2.9 days) and common (6.7 per day). Shaving just 0.2 days off each = significant capacity saved. Questions: Do they need approval? Can you pre-approve common ones? Are requirements unclear? Create templates.
- 🔴 **Stop ignoring low-priority tickets:** Set a rule: if a low-priority ticket hasn't been touched in 5 days, automatically escalate to Medium-priority. Prevents the "ignore then panic" cycle.
- 🟢 **Study incident process:** It works. Fastest resolution (2.7) despite highest daily volume (8.9). Document the process: decision tree? escalation path? dedicated team? Apply to other types.
- 🟢 **Preventive work:** If same issue keeps recurring, fix the root problem instead of just fixing tickets each time.

---

#### 4️⃣ Page 4 - Team Performance

<p align="center">
  <img src="Images/Queue_Performance.png" width="100%">
</p>

**What The Data Shows:**

**Best Performing Teams** (Fast + Low Miss Rate):
- **Service Outages:** 30.09% missed rate, 2.43 days average ✅ BEST
- **Technical Support:** 42.09% missed rate, 2.65 days average ✅ GOOD
- **IT Support:** 53.85% missed rate, 2.51 days average

**Worst Performing Teams** (Slow + High Miss Rate):
- **Human Resources:** 88.78% missed rate, 3.34 days average ❌ WORST
- **General Inquiry:** 83.33% missed rate, 3.29 days average ❌ BAD
- **Customer Service:** 81.87% missed rate, 3.07 days average ❌ BAD

**Ticket Volume:**
- **Technical Support** handles most by far: 3,412 tickets
- **Customer Service:** 1,859 tickets
- **General Inquiry:** only 168 tickets

The key point: **Technical Support handles the most work but has the SECOND-BEST miss rate.** This proves volume is NOT the problem.

**Why This Matters:**
- **Two completely different clusters:** One group (Service Outages, Tech Support, IT Support) are both fast AND compliant. Another group (HR, General Inquiry, Customer Service) are both slow AND non-compliant. This is not random - something fundamental is different between them.
- **Technical Support proves it's possible:** Handle 3,412 tickets (most of anyone) yet stay at 42% miss rate. If they can do it, others can too.
- **Small teams fail too:** HR only has 205 tickets but 88.78% miss rate. Staffing is not the issue.

**What To Do:**
- 🔴 **Human Resources is your #1 priority:** Only 205 tickets but worst performance. (1) Interview the team - what's the biggest obstacle? (2) Compare their process to Technical Support - what are they doing differently? (3) Check if they're trained to handle their ticket types. (4) Look at last 10 tickets - where did time go?
- 🔴 **General Inquiry needs reorganization:** "General" queues are vague. (1) Define what SHOULD go here. (2) What should route elsewhere? (3) Create clear rules. (4) Re-train team. (5) Better routing = tickets get to right team faster.
- 🟡 **Customer Service needs dual fix:** Both volume issue (1,859 tickets) AND process issue (miss rate too high). (1) Check staffing vs. target. (2) If understaffed, hire. (3) Also audit process for inefficiencies.
- 🟢 **Share best practices:** Have Service Outages and Technical Support teach other teams. What's working? What process steps are critical? Document and standardize.

---

#### 5️⃣ Page 5 - Geographic Performance

<p align="center">
  <img src="Images/Locations.png" width="100%">
</p>

**What The Data Shows:**

**Ticket Volume by Country:** All 10 European countries get similar volume (1,140-1,240 tickets each):
- **Belgium:** ~1,240 (highest)
- **Austria:** ~1,170
- **Others:** All in similar range

**Resolution Speed by Country:**
- **Austria:** FASTEST ✅
- **Netherlands & Spain:** SLOWEST ⚠️
- **Others:** In the middle

**Resolution Time Pattern:**
- **2-day resolutions:** Most common, 8.77 daily average
- **3-day resolutions:** 6.98 daily average  
- **4-day resolutions:** Least common, 4.61 daily average

**Time Period:** 2024 has **70.83%** of tickets, 2025 has **29.17%** (partial year).

**Why This Matters:**
- **Equal volume across countries = NO excuse for poor performance.** Netherlands gets same workload as Austria but resolves much slower. Not a workload issue - a process/staffing issue.
- **Austria is proof it's achievable.** Same resources as others, but best speed. Their approach should become company standard.
- **Netherlands and Spain being slow across ALL ticket types** suggests company-wide issue (staffing, tools, process) not specific to one team.

**What To Do:**
- 🔴 **Netherlands & Spain need urgent help:** Both consistently slow. (1) Check if understaffed vs. company standard. (2) If yes, hire. (3) Compare their process to Belgium/Austria - what's different? (4) Check their tools - are they slower? (5) Send Austria team to audit and train them.
- 🟢 **Austria = the model:** (1) Document their complete process. (2) Have them lead training for other countries. (3) Use their speed (probably 2.5-2.6 days) as the company target, not 2.82 days.
- 🟡 **Standardize globally:** Same SLA, same process, same tools across all countries. Don't let different regions have different standards.
- 🟡 **Consider legitimate factors:** If Netherlands/Spain have real constraints (time zones, language, staffing market), acknowledge them. Adjust SLA or add resources accordingly. Don't set impossible targets.
- 🟢 **Monthly knowledge sharing:** Country leads on video call share what works/doesn't. Best practices from one country spread to others.

---

## 🔎 Final Conclusion & Recommendations

📍 Key Takeaways:

✔️ **A 61.66% SLA Breach rate is the single most urgent issue in the entire system.** Nearly two-thirds of all tickets are resolved outside SLA - this is not a minor inefficiency but a fundamental service delivery failure that requires structural intervention, not just monitoring.

✔️ **The problem is not volume - it is queue and process quality.** Technical Support handles the most tickets (3,412) yet maintains one of the lowest breach rates (42.09%). Meanwhile, Human Resources (205 tickets, 88.78% breach) and General Inquiry (168 tickets, 83.33% breach) struggle despite low volume. More tickets do not cause more breaches - poor processes do.

✔️ **Low-priority tickets are a hidden SLA drain.** Representing 44.44% of MTTR distribution, low-priority tickets are routinely deprioritized until they breach SLA. Setting maximum age limits and auto-escalation rules for aging low-priority tickets would materially reduce overall breach rates.

✔️ **Geographic performance gaps need country-level action plans.** Netherlands and Spain consistently show higher MTTR across all ticket types, while Austria resolves fastest. A structured knowledge-sharing program from high-performing to low-performing countries is a low-cost, high-impact lever.

✔️ **Incident management is working - scale the model.** Incidents have the highest daily volume yet the lowest MTTR (2.7 days), proving the triage and escalation process works. Applying the same discipline to Request and Problem ticket workflows is the clearest path to reducing overall MTTR and SLA breach rates system-wide.
