![Power BI](https://img.shields.io/badge/Tool-Power_BI-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)

---

<p align="center">
  <img src="Images/banner.jpg" width="100%">
</p>

# 📊 IT Helpdesk Operations & SLA Optimization | Power BI

_An interactive Power BI Star Schema dashboard designed to monitor helpdesk operational throughput, identify queue bottlenecks, and remediate systemic SLA compliance gaps._

- 🎯 **Business Question:** How can we optimize ticket resolution workflows (MTTR) and systematically reduce the critical 61.66% SLA breach rate across queues and regional boundaries?
- 🏬 **Domain:** IT Operations / Service Management (ITSM) / Corporate Infrastructure
- 🛠️ **Tools:** Power BI

👤 Author: Bạch Minh Nam

---

## 📑 Table of Contents
1. [📌 Background & Overview](#-background--overview)
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)
3. [🧠 Design Thinking Process](#-design-thinking-process)
4. [📊 Key Insights & Visualizations](#-key-insights--visualizations)
5. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview

### Objective

As global organizations expand, managing internal and external technical support efficiently becomes vital to maintaining enterprise productivity. Fragmented ticket lifecycles frequently obfuscate true operational capacity and delay critical response times.

The IT Support Director and Operations Managers need a dashboard to answer 3 main questions:

✔️ **Service Efficiency (MTTR):** What is our average time to resolve tickets, and which categories or queues face systemic bottlenecks?

✔️ **SLA Compliance:** What factors are driving our severe 61.66% SLA breach rate, and how does performance vary across days of the week?

✔️ **Resource & Geographic Optimization:** How are ticket workloads distributed among our 10 operational countries, and where should support engineering capacity be reallocated?

This project centralizes disconnected transaction records into an optimized analytical model. By uncovering granular performance metrics, it empowers leadership to implement data-driven capacity scheduling, streamline ticket routing, and restore SLA accountability.

### 👤 Who is this project for?

✔️ **IT Support Director** - Needs a macro-level overview of overall system health, global operational throughput, and high-level SLA metrics to justify corporate resource distribution and headcount budgets.

✔️ **Helpdesk & Queue Managers** - Require granular tracking of specific functional queues (e.g., Technical Support, HR, Customer Service) to balance technician workloads, detect aging items, and enforce team accountability.

✔️ **Regional Operations Leads** - Need localized spatial and country-specific insights to address regional process gaps and resolve capacity disparities between countries.

---

## 📂 Dataset Description & Data Structure

## 📌 1. Data Source & Original Structure
* **Source:** IT Support Ticket Desk 
* **Format:** Flat Excel file (`.xlsx`)
* **Volume:** 1 single sheet (`Data`) containing **11,923 rows × 20 columns**
* **Original Grain:** 1 row = 1 ticket

### 🗂️ Original Data Dictionary
Before transformation, the raw flat dataset mixed transactional ticket information, routing attributes, geographic coordinates, and multiple category tags into a single table:

| Column Name | Description |
| :--- | :--- |
| **Ticket ID** | Unique identifier for each ticket |
| **Date** | Date the ticket was created |
| **Resolution Date** | Calculated expected resolution date |
| **Subject** | Subject line of the support ticket |
| **Body** | Main body of the ticket request or issue |
| **Answer** | Support response or follow-up provided |
| **Type** | Type of request (e.g., Request, Problem) |
| **Queue** | Support queue handling the ticket |
| **Priority** | Priority level assigned to the ticket |
| **Primary Tag** | Primary classification of the ticket |
| **Secondary Tag** | Secondary classification for added context |
| **Category Tag** | General category tag for classification |
| **Technical Tag** | Specific technical classification tag |
| **Status Tag** | Status or nature of the issue |
| **Resolution Tag** | Assigned resolution classification |
| **Documentation Tag** | Documentation reference tag |
| **Additional Tag** | Additional contextual or tagging info |
| **Country** | Country of origin for the ticket |
| **Latitude** | Latitude of the originating location |
| **Longitude** | Longitude of the originating location |

---

## ⚙️ 2. Data Processing & Modeling Decisions
To convert this flat file into a high-performance **Star Schema** within Power BI, the original structure was modified through the following architectural decisions:

### A. Extracting Standard Dimension Tables
Purely descriptive string columns were decoupled from the fact table into normalized dimension tables to reduce redundancy, maximize file compression, and optimize filtering performance:

| Target Dimension | Original Column(s) | Transformation Reason & Logic |
| :--- | :--- | :--- |
| **Dim_Queue** | `Queue` | Prevents repetitive string storage; optimizes slicing/filtering performance. |
| **Dim_Priority** | `Priority` | Standardizes and restricts values into 3 clean categories: *Low / Medium / High*. |
| **Dim_Type** | `Type` | Standardizes data quality into 4 distinct request types: *Request / Problem / Incident / Change*. |
| **Dim_Country** | `Country`, `Latitude`, `Longitude` | Consolidates related spatial attributes into a singular geographic lookup dimension. |

### B. Advanced Tag Handling (Many-to-Many Normalization)
The raw dataset contained **8 separate tag columns** (`Primary`, `Secondary`, `Category`, `Technical`, `Status`, `Resolution`, `Documentation`, `Additional`). Since a single ticket can hold multiple tag designations, mapping them horizontally causes data sparsity and restricts reporting flexibility.
1. **Unpivot Process:** Unpivoted all 8 tag columns into two standardized key-value columns: `Tag Name` and `Tag Type`.
2. **Dimension Creation (`Dim_Tag`):** Removed duplicates from the unpivoted columns and assigned a unique surrogate `Tag ID` to build a centralized tag dictionary.
3. **Bridge Table (`Bridge_Ticket_Tag`):** Constructed a junction table mapped with `Ticket ID` and `Tag ID`. This cleanly resolves the **Many-to-Many relationship** between tickets and tags while strictly preserving the transactional grain of the fact table (**1 row = 1 ticket**).

### C. Time Intelligence
* **Dim_Date:** A dedicated calendar table generated via DAX, established with a 1-to-many relationship linking back to the `Date` column inside the fact table to unlock complex time-intelligence calculations (YoY, MoM, rolling totals).

---

## 📊 3. Final Star Schema Structure & Relationships

### 1️⃣ Tables Used
The finalized data model consists of **8 total tables** (1 Fact table, 1 Bridge table, 6 Dimension tables):
* **Fact_Ticket** - Holds core transactional details, messages, answers, and time metrics (Grain: 1 row = 1 unique ticket).
* **Dim_Tag** - The comprehensive consolidated tag directory.
* **Bridge_Ticket_Tag** - Intersection bridge to facilitate many-to-many tag relations.
* **Dim_Date** - DAX calendar dimensions.
* **Dim_Queue / Dim_Priority / Dim_Type / Dim_Country** - Outrigger lookup metrics used to slice analytical charts.

### 2️⃣ Core Table Schemas

#### Table: Fact_Ticket (Fact Table)
| Column Name | Description |
| :--- | :--- |
| Ticket ID | **Primary Key.** Unique identifier for each support ticket. |
| Subject | Subject line of the support ticket. |
| Body | Main body text detailing the customer's request or issue. |
| Answer | Support response or resolution text provided to the user. |
| Date | Date the ticket was created (Foreign Key to `Dim_Date`). |
| Resolution Date | Calculated expected resolution date. |
| Queue ID / Priority ID / Type ID / Country ID | Foreign Keys linking to respective lookup dimension tables. |

#### Table: Dim_Tag (Dimension Table)
| Column Name | Description |
| :--- | :--- |
| Tag ID | **Primary Key.** Unique auto-generated identifier for each distinct tag. |
| Tag Name | The specific tag string value (e.g., "Integration", "Billing", "Sales"). |
| Tag Type | The original functional column category of the tag (e.g., Primary, Technical, Status). |

#### Table: Bridge_Ticket_Tag (Bridge Table)
| Column Name | Description |
| :--- | :--- |
| Ticket ID | Foreign Key linking back to `Fact_Ticket`. |
| Tag ID | Foreign Key linking back to `Dim_Tag`. |

### 3️⃣ Data Relationships
* **Dim_Date** → **Fact_Ticket**: 1-to-Many (joined on `Date`)
* **Dim_Queue / Dim_Priority / Dim_Type / Dim_Country** → **Fact_Ticket**: 1-to-Many (joined on respective dimension IDs)
* **Fact_Ticket** → **Bridge_Ticket_Tag**: 1-to-Many (joined on `Ticket ID`)
* **Dim_Tag** → **Bridge_Ticket_Tag**: 1-to-Many (joined on `Tag ID`)

<p align="center">
  <img src="Images/data_model.png" width="80%">
</p>

## 🧠 Design Thinking Process

This project followed the Design Thinking framework across 3 main steps: Empathize, Define Point of View, and Ideate.

### 1️⃣ Empathize - Understanding the Stakeholder

| Question | Answer |
|---|---|
| **Who views this dashboard?** | IT Support Director and Operations Managers |
| **What problem does it solve?** | Resolves high SLA breach rates, excessive resolution lag, and poor visibility across decentralized technical queues. |
| **When & where is it used?** | Reviewed weekly during cross-department operational updates and utilized daily via workstations for active workload tracking. |
| **Why is this analysis needed?** | High volume combined with unmonitored ticket lifecycles degrades employee productivity and compromises service quality. |
| **How do they decide?** | By isolating top breach queues, identifying regional performance delays, and reallocating technicians based on type-specific volume. |
| **Pains** | A critical 61.66% SLA breach rate, unquantified ticket aging in low-priority tiers, and isolated functional queues. |
| **Gains** | Centralized end-to-end performance tracking, standardized metrics, and granular action paths via interactive drill-through views. |
| **Key Questions to Answer** | • What is the overall intake trend and monthly volume of incoming helpdesk tickets?<br>• Which queues and issue categories act as the primary drivers of SLA breaches?<br>• How do resolution times (MTTR) distribute across priority levels and request types?<br>• Are there operational or capacity imbalances across different days of the week?<br>• Which geographic regions exhibit critical resolution lag and require workflow audits? |

### 2️⃣ Define Point of View - Choosing the Right Angles

| Point of View | Description | Why the stakeholder cares |
|---|---|---|
| **Operational Health Perspective** | Focuses on tracking macro system performance metrics including total volume, system MTTR, and MoM% workload variations. | Essential for understanding structural capacity stability and determining if systemic adjustments are required. |
| **Compliance & Workflow Audit** | Targets the distribution of SLA breaches across structural attributes such as ticket priorities, types, and operational queues. | Crucial for isolating explicit failure points (e.g., HR queue's 88.78% breach rate) to execute process engineering. |
| **Geographic Efficiency Mapping** | Assesses country-level operational performance, isolating localized throughput variations and cross-border volume imbalances. | Identifies specific regional resource constraints and enables the transfer of efficient models (e.g., Austria) to slower regions. |

**Northstar Metrics:**

| Northstar 1 | Northstar 2 |
|---|---|
| **Mean Time To Resolution (MTTR)** | **SLA Breach Rate (%)** |
| Formula: `AVERAGE(Fact_Ticket[Resolution Days])` | Formula: `DIVIDE(CALCULATE(COUNT(Fact_Ticket[Ticket ID]), Fact_Ticket[Is Breached]=1), COUNT(Fact_Ticket[Ticket ID]), 0)` |
| Success when: System MTTR scales below the 2.50-day operational target. | Success when: Overall SLA breaches fall from 61.66% to under a 15.0% ceiling. |
| Why this metric: Represents the primary indicator of helpdesk response velocity and overall operational throughput. | Why this metric: Directly quantifies support delivery reliability and compliance with enterprise service agreements. |

### 3️⃣ Ideate - Structuring the Dashboard

| | **Page 1: Helpdesk Overview** | **Page 2: Ticket Types & Queues** | **Page 3: Resolution & Spatial Insights** |
|---|---|---|---|
| **Layer 0 (Scorecards)** | Total Tickets, MTTR, SLA Breach %, Target Comparisons, MoM % growth rates. | Ticket Volume by Category, Active Queue Distribution counts. | Average Daily Ticket counts, Total Open/Closed metrics. |
| **Layer 1 (1-dimension breakdown)** | Monthly incoming ticket intake volume trends, Ticket volume distribution by Priority. | Top 5 Primary Ticket Tags, Overall SLA Breach volumes by Day of the Week. | Total Tickets grouped by Year, Country-level spatial volume map layouts. |
| **Layer 2 (2-dimension breakdown)** | Ticket volume MoM% growth matrices distributed simultaneously by Ticket Type. | Joint Queue Performance Matrix (MTTR vs SLA Breach Rate), Queue Compliance comparisons. | Two-dimensional Calendar Heatmaps (Day x Month), Type × Country MTTR performance grids. |

---

## ⚒️ Main Process

1️⃣ **Connect & Load Data** - Establishes automated data connections to the raw Excel workbook, validates data type mappings for regional datetime configurations, and loads the data into the Power Query environment.

2️⃣ **Data Modeling** - Executes the unpivoting of the 8 disparate horizontal tag structures, runs duplicate extraction routines to create clean lookup dimensions, builds a many-to-many bridge junction table, and maps all relationships into a highly efficient Star Schema.

3️⃣ **DAX Measures** - Engineers explicit, calculated metrics to evaluate total transaction counts, calculated network resolution durations, dynamic Month-over-Month volume variances, and strict conditional logic boundaries for SLA breach tracking.

4️⃣ **Power BI Visualization** - Structures the report interface utilizing advanced interaction features, including dynamic bookmark-driven sliding filter panels, customized informational tooltips, and complete cross-page drill-through evaluation tables.

---


## 📊 Key Insights & Visualizations

### 🔍 Dashboard Preview

> **⚙️ Advanced Power BI Features Used**
>
> - 🔖 **Bookmarks & Button** - A hidden filter panel is toggled by clicking the filter icon on the bottom-left of every page. The panel slides in with: Date Range slider (1/1/2024 – 6/4/2025), and slicers for Year-Month, Queue, Priority, and Country - applied simultaneously across the report page.
>
> <p align="center"><img src="Images/Bookmarks.png" width="40%"></p>
>
> - 🔍 **Drill Through** - Click on any data point across all pages to drill into the `Drill_Though` detail page, showing a full ticket-level table with Ticket ID, Priority, Queue, Primary Tag, Status Tag, Created Date, Resolution Date, and Resolution Days.
>
> <p align="center"><img src="Images/Drill_Though.png" width="100%"></p>
>
> - 💬 **Tooltips** - Hover over data points on trend charts to reveal additional context without cluttering the main view.
>
> <p align="center"><img src="Images/tooltip1.png" width="48%"> <img src="Images/tooltip2.png" width="48%"></p>
>
> - 🔙 **Back Button** - A navigation arrow on the top-left of the Drill Through page returns the user to the previous report page.

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

✔️ **Incident management is working - scale the model.** Incidents have the highest daily volume yet the lowest MTTR (2.7 days), proving the triage and escalation process works. Applying the same discipline to Request and Problem ticket workflows is the clearest path to reducing overall MTTR and SLA breach rates system-wide.
