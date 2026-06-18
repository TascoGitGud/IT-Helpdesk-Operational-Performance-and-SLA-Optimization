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
1. [📌 Background & Overview](#-background--overview)
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)
3. [🧠 Design Thinking Process](#-design-thinking-process)
4. [📊 Key Insights & Visualizations](#-key-insights--visualizations)
5. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview

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

## 📂 Dataset Description & Data Structure

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

## 🧠 Design Thinking Process

This project followed the Design Thinking framework across 3 main steps: Empathize, Define Point of View, and Ideate.

> 📄 For the full Design Thinking breakdown, see [Fashion Marketing & Sales Analysis - Design Thinking.pdf](IT Helpdesk Analysis - Design Thinking.pdf)

### 1️⃣ Empathize - Understanding the Stakeholder

| Question | Answer |
|---|---|
| **Who views this dashboard?** | Marketing Manager, Sales Manager, CEO/Board of Directors |
| **What problem does it solve?** | Need a single dashboard to monitor business and marketing performance — know which campaigns are profitable, which channels work, and which products to prioritize |
| **When & where is it used?** | Daily for spending & revenue tracking; weekly for campaign reviews; on laptop or large screen during meetings |
| **Why is this analysis needed?** | Marketing budget is limited — need to know if every dollar spent is generating profit and avoid pouring money into inefficient campaigns or products |
| **How do they decide?** | By comparing ROAS and Profit Margin across campaigns, channels, and products — then deciding to scale or cut |
| **Pains** | Data is scattered across Facebook Ads, order systems, and manual Excel sheets; hard to compare SKU-level and channel-level performance |
| **Gains** | Full picture in one screen; faster budget allocation decisions; reduced marketing waste |
| **Key Questions to Answer** | • What is total revenue and profit this month?<br>• Which campaigns have the best ROAS?<br>• Is Ads or Direct Sales more profitable?<br>• Which products/categories have low or negative margin?<br>• What % of budget has been spent — and is it generating profit? |

### 2️⃣ Define Point of View - Choosing the Right Angles

| Point of View | Description | Why the stakeholder cares |
|---|---|---|
| **Overview** | Total revenue, costs, profit, and ROAS for the month | To see the big picture before diving into details |
| **Campaign** | ROAS, CPC, CPM, CTR, Impressions per campaign | To know which campaigns to scale or cut |
| **Channel** | Ads Sales vs Direct Sales by revenue, margin, and region | To know which channel drives more profitable revenue |
| **SKU** | Revenue, profit, and margin by category and product | To know which products to grow and which to fix |

**Northstar Metrics:**

| Northstar 1 | Northstar 2 |
|---|---|
| **ROAS** | **Profit Margin (%)** |
| Formula: `ROAS = Total Ads Revenue / Total Marketing Cost` | Formula: `Profit Margin = Gross Profit / Net Revenue` |
| Success when: ROAS > 10 and growing vs. prior period | Success when: Profit Margin stays stable or improves alongside revenue growth |
| Why this metric: Directly measures how much revenue each dollar of ad spend returns — the core signal for budget decisions | Why this metric: High revenue doesn't guarantee profit — ensures growth is healthy, not just volumetric |

### 3️⃣ Ideate - Structuring the Dashboard

| Layer | Overview | Campaign | Channel | SKU |
|---|---|---|---|---|
| **Layer 0 (Scorecards)** | Total Revenue, Total Orders, Marketing Cost, ROAS, Total Units Sold | Total Revenue, Total Orders, Marketing Cost, ROAS, Total Units Sold | Total Revenue, Total Orders, Marketing Cost, ROAS, Total Units Sold | Total Revenue, Total Orders, Marketing Cost, ROAS, Total Units Sold |
| **Layer 1 (1-dimension)** | Revenue & Cost by week/day; Profit & Revenue trend; Ads vs Direct ratio | ROAS by week/day; CPC & CPM by day; Impressions & CTR | Revenue by day (Ads vs Direct); Revenue by Customer Tier; ROAS by channel | Profit & Revenue by category; Profit Margin by category; Ads Revenue by category |
| **Layer 2 (2-dimension)** | Marketing Cost vs Revenue by campaign; % Budget used | Weekly table: ROAS, Spend, Impressions, CTR, CPC; Metric selector | Table: Ads/Direct × Revenue, Orders, Margin, Units, Contribution; Revenue & Profit by product | SKU table: Spend, Profit, ROAS, ROI, Revenue Contribution; Profit Margin × Gross Profit matrix |
| **What's missing?** | MoM comparison | ROAS per individual campaign | New vs. returning customers | Inventory vs. sales crosscheck |

---

## ⚒️ Main Process

1️⃣ **Load Data** - Connected Power BI to the ticket file, checked all data types, organized in Power Query.

2️⃣ **Build Data Model** - Took flat structure and organized it into separate clean tables, unpivoted the tags, created bridge table, built Star Schema.

3️⃣ **Create Formulas** - Wrote formulas to calculate total tickets, average resolution time, breach rate, month-over-month changes, and team performance.

4️⃣ **Build Dashboard** - Created 5 interactive pages with filters, hover tooltips, drill-down details, and navigation buttons.

---

## 📊 Key Insights & Visualizations

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

#### 1️⃣ Page 1 - Overview

<p align="center">
  <img src="Images/Overview.png" width="100%">
</p>

📌 **Analysis 1:**

- **Observation:** The helpdesk handled **11,923 total tickets** (+6.3% vs. last month) with an MTTR of **2.82 days** - right at the SLA target boundary. The most alarming metric is **% SLA Breach at 61.66%**, meaning nearly 2 in 3 tickets are resolved outside the agreed timeframe. Monthly ticket volume fluctuated around an average of **701 tickets/month**, dipping to a low of 617 in January 2025 before recovering to 707 in May 2025. By priority, **medium tickets dominate (41.53%)**, followed by high (38.34%) and low (20.13%). All ticket types showed positive MoM growth (5.85%–6.71%), indicating consistently rising workload.

- **Recommendation:**
  - 🔴 **SLA Breach at 61.66% is critical and requires immediate action.** Over half of all tickets are resolved late - this is a systemic issue, not an isolated one. A root-cause review across queues and ticket types is the first priority.
  - 🟡 **Investigate the Jan 2025 volume drop (617 tickets).** An unusual dip may indicate underreporting, a system issue, or a genuine reduction in requests - understanding the cause helps validate data reliability.
  - 🟢 **Monitor the MoM growth trend closely.** All ticket types are growing at ~6% monthly - capacity planning should be adjusted proactively before volume outpaces team bandwidth.

---

#### 2️⃣ Page 2 - Ticket Types

<p align="center">
  <img src="Images/Ticket_Types.png" width="100%">
</p>

📌 **Analysis 2:**

- **Observation:** **Incident tickets lead at 38.93%** of total volume, followed by Request (29.34%), Problem (20.95%), and Change (10.78%). By primary tag, **Technical issues are the most frequent (3.1K)**, with Security second (2.0K) - both also showing the highest ticket growth rates (6.07% and 5.89%). Among queues, **Customer Service has the highest SLA Breach at 81.87%** despite not being the busiest queue, while **Technical Support - the largest queue (3,412 tickets) - has a relatively lower breach rate at 42.09%**. SLA Breach rates are consistently high across all days of the week (60–63%), with **Sunday being the worst at 63.06%**.

- **Recommendation:**
  - 🔴 **Customer Service queue needs urgent attention.** 81.87% SLA Breach on 1,859 tickets is the worst among high-volume queues - review staffing levels, escalation paths, and resolution workflows specific to this queue.
  - 🟡 **Weekend SLA performance is slightly worse - consider weekend coverage.** Sunday breach rate (63.06%) is the highest of the week, suggesting support capacity drops on weekends relative to incoming volume.
  - 🟢 **Technical Support is managing scale relatively well (42.09% breach).** As the largest queue, its comparatively lower breach rate suggests better processes - these practices should be documented and replicated in underperforming queues.

> 📋 For ticket-level detail by type, queue, and tag - use the **Drill Through** feature on any data point.

---

#### 3️⃣ Page 3 - Resolution Times

<p align="center">
  <img src="Images/Resolution_Times.png" width="100%">
</p>

📌 **Analysis 3:**

- **Observation:** Average Daily Tickets stand at **22.88** with an overall MTTR of **2.82 days** and SLA Breach at **61.66%**. Breaking down by ticket type, **Incident tickets have the highest average daily volume (8.9) but the lowest MTTR (2.7 days)**, suggesting the team prioritizes and resolves incidents quickly. **Request tickets take the longest (MTTR 2.9)** despite moderate daily volume (6.7). By priority, **low-priority tickets account for the largest MTTR share (44.44%)**, indicating they are frequently deprioritized and left to age. The heatmap shows ticket volume is heaviest in the **Sep–Dec 2024 period**, with lighter loads in early 2024 and early 2025.

- **Recommendation:**
  - 🔴 **Low-priority tickets are aging unacceptably.** At 44.44% of MTTR distribution, low-priority tickets are contributing significantly to overall SLA breach - set maximum age thresholds to prevent indefinite deferral.
  - 🟡 **Request-type tickets need process streamlining.** Highest MTTR (2.9 days) with substantial daily volume (6.7) - review whether request workflows have unnecessary approval steps causing delays.
  - 🟢 **The incident handling model is working - replicate it.** Lowest MTTR (2.7) despite highest daily volume shows effective triage and escalation. Apply the same prioritization logic to Request and Problem queues.

---

#### 4️⃣ Page 4 - Queue Performance

<p align="center">
  <img src="Images/Queue_Performance.png" width="100%">
</p>

📌 **Analysis 4:**

- **Observation:** The Queue Performance Matrix clearly separates queues into two clusters. **High-breach queues** (Human Resources 88.78%, General Inquiry 83.33%, Customer Service 81.87%) all show MTTR above 3.0 days - slow and non-compliant. **Low-breach queues** (Service Outages & Maintenance 30.09%, Technical Support 42.09%, IT Support 53.85%) resolve tickets faster (MTTR 2.43–2.65 days). **Technical Support is by far the highest-volume queue (3,412 tickets)** but maintains one of the best breach rates. The queue comparison chart confirms that SLA Compliant tickets dominate in Technical Support, while breach tickets dominate in HR and General Inquiry.

- **Recommendation:**
  - 🔴 **Human Resources queue is the worst performer (88.78% breach, MTTR 3.34).** With only 205 tickets, this is not a volume problem - it is a process or staffing problem. Immediate intervention is needed.
  - 🟡 **General Inquiry (83.33% breach) likely lacks clear ownership.** Vague ticket categorization often leads to delays - define clear routing rules and assign dedicated owners for this queue.
  - 🟢 **Use Technical Support and Service Outages as benchmarks.** Both handle meaningful volume with low breach rates - conduct a process audit on these queues and share best practices with underperforming ones.

> 📋 Click any queue bar to drill through to individual ticket records for deeper investigation.

---

#### 5️⃣ Page 5 - Locations

<p align="center">
  <img src="Images/Locations.png" width="100%">
</p>

📌 **Analysis 5:**

- **Observation:** The dataset covers **10 European countries**, with **Belgium generating the most tickets** (~1.23–1.24K range) and **Austria having the lowest MTTR** - making it the fastest-resolving country. Ticket volume is relatively evenly distributed across countries (1.14K–1.24K each), suggesting consistent global demand. The MTTR heatmap by Type and Country shows **Netherlands and Spain tend to have darker shades across all ticket types**, indicating slower resolution times. Tickets resolved in **2 days account for the highest average daily ticket count (8.77)**, while 4-day resolutions have the fewest (4.61) - confirming most tickets are being resolved within 3 days when compliant. 2024 dominates total ticket volume at **70.83%** vs 29.17% for 2025 (partial year).

- **Recommendation:**
  - 🔴 **Investigate Netherlands and Spain for systemic resolution delays.** The MTTR heatmap shows consistently darker cells across all ticket types - this points to a country-level staffing or process gap, not a ticket-type-specific issue.
  - 🟡 **Study Austria's low-MTTR model and replicate it.** As the fastest-resolving country, Austria's local processes, team structure, or escalation paths may offer replicable lessons for slower countries.
  - 🟢 **Standardize SLAs and workflows across all countries.** Even distribution of ticket volume means no country has a significantly higher workload - performance differences are driven by process quality, not scale.

---

## 🔎 Final Conclusion & Recommendations

📍 Key Takeaways:

✔️ **A 61.66% SLA Breach rate is the single most urgent issue in the entire system.** Nearly two-thirds of all tickets are resolved outside SLA - this is not a minor inefficiency but a fundamental service delivery failure that requires structural intervention, not just monitoring.

✔️ **The problem is not volume - it is queue and process quality.** Technical Support handles the most tickets (3,412) yet maintains one of the lowest breach rates (42.09%). Meanwhile, Human Resources (205 tickets, 88.78% breach) and General Inquiry (168 tickets, 83.33% breach) struggle despite low volume. More tickets do not cause more breaches - poor processes do.

✔️ **Low-priority tickets are a hidden SLA drain.** Representing 44.44% of MTTR distribution, low-priority tickets are routinely deprioritized until they breach SLA. Setting maximum age limits and auto-escalation rules for aging low-priority tickets would materially reduce overall breach rates.

✔️ **Geographic performance gaps need country-level action plans.** Netherlands and Spain consistently show higher MTTR across all ticket types, while Austria resolves fastest. A structured knowledge-sharing program from high-performing to low-performing countries is a low-cost, high-impact lever.
