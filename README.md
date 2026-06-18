![Power BI](https://img.shields.io/badge/Tool-Power_BI-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)

---

<p align="center">
  <img src="Images/banner.jpg" width="100%">
</p>

# 📊 IT Helpdesk Operational Performance & SLA Optimization | Power BI

_An interactive enterprise-grade analytical dashboard engineered using an optimized Star Schema data model to audit ticket support lifecycles, eliminate functional queue backlogs, and systematically remediate SLA breaches._

- 🎯 **Business Question:** How can we minimize our SLA breach rate, and Mean Time to Resolve (MTTR) across multiple functional queues and international locations?
- 🏬 **Domain:** IT Service Management (ITSM) / Enterprise Operations
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

### Objective

The IT Manager (supported by the Service Desk Lead and Executive Board) needs a dashboard to answer 3 main questions:

✔️ **Operational Baseline:** What are our current total ticket volumes, baseline resolution speeds, and overall monthly influx trends?

✔️ **SLA Compliance Bottlenecks:** Which support queues are experiencing the worst SLA non-compliance issues, and where are systemic delays occurring?

✔️ **Ticket Classification Impact:** Do Incident or Request tickets demand longer resolution intervals, and which ticket categories are driving the highest workloads?

A centralized analytics solution that unifies disjointed data to optimize staff scheduling, resource allocation, and reduce service delivery failures.

### 👤 Who is this project for?

✔️ **IT Support Manager & Service Desk Lead** - Need to monitor active backlogs, track real-time queue bottlenecks, and audit team-wide resolution speeds against established SLA agreements.

✔️ **Executive Board & Directors** - Require clear macro-level transparency into global service quality, SLA success ratios, and factual data evidence to evaluate organizational operational health and justify resource allocation.

✔️ **Queue & Regional Operations Leads** - Need localized spatial insights and functional category analyses to identify specific technician constraints and reallocate engineering capacity across geographical borders.

---

## 📂 Dataset Description & Data Structure

## 📌 1. Data Source & Original Structure
* **Source:** IT_Support_Ticket_Dataset 
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
| **Who views this dashboard?** | IT Manager, Service Desk Lead, and Executive Board. |
| **What problem does it solve?** | Centralizes disjointed platform logs, identifies localized helpdesk bottlenecks, and uncovers root causes for severe SLA non-compliance. |
| **When & where is it used?** | Accessed daily via laptops to monitor new arrivals and tracking metrics, and analyzed weekly on large displays during cross-team alignment reviews. |
| **Why is this analysis needed?** | Stakeholders are highly concerned about continuous, systemic SLA breaches without knowing the operational starting point for process improvements, and they require empirical data evidence to present to executive management. |
| **How do they decide?** | By checking performance variations across queues, issue types, and geographic locations to reallocate technical headcount or re-engineer routing paths. |
| **Pains** | • Siloed, disconnected reports across various systems.<br>• Zero macro visibility into ticket concentrations or queue bottlenecks.<br>• High SLA breach rates without transparent, root-cause diagnostics. |
| **Gains** | • Single-pane clarity to pinpoint exact system bottlenecks instantly.<br>• Data-backed workforce distribution to maximize operational efficiency.<br>• Systematic, measurable reductions in critical SLA failure rates. |
| **Key Questions to Answer** | • What are the overall cumulative ticket volumes and current baseline MTTR?<br>• Which functional queue is experiencing the most severe SLA breaches?<br>• Which ticket classification or request type demands the longest resolution time?<br>• Which country or operating location presents the poorest resolution efficiency?<br>• Are monthly incoming ticket volume trends expanding or contracting over time? |

### 2️⃣ Define Point of View - Choosing the Right Angles

| Point of View | Description | Why the stakeholder cares |
|---|---|---|
| **Operational Health Overview** | Evaluates macro performance parameters including cumulative ticket ingestion, total MTTR, and temporal baseline charts. | Establishes a foundational, comprehensive baseline view of system wellness before navigating down to micro attributes. |
| **Granular Workload & Category Analytics** | Segments support volumes and breach ratios simultaneously by request types, assigned priority tiers, and explicit ticket tags. | Highlights precisely which operational areas comprise the highest volume scale and identify specific categories generating compliance risk. |
| **Functional Queue & Spatial Performance** | Directly compares response speeds, volumetric counts, and compliance matrices between distinct corporate teams and regional borders. | Guides management on exactly which specific queues require additional staff scaling and uncovers regional training or process gaps. |

**Northstar Metrics:**

| Northstar 1 | Northstar 2 |
|---|---|
| **% SLA Breach** | **Mean Time to Resolve (MTTR)** |
| Formula: `DIVIDE(CALCULATE(COUNT(Fact_Ticket[Ticket ID]), Fact_Ticket[IsBreached] = 1), COUNT(Fact_Ticket[Ticket ID]), 0)` | Formula: `AVERAGE(Fact_Ticket[Resolution Days])` |
| Success when: The system-wide breach percentage drops below 50% and sustains a continuous monthly decline. | Success when: MTTR decreases steadily month-over-month, maintaining a position safely below the 3-day target threshold. |
| Why this metric: Acts as the definitive proxy for service desk delivery quality; high breach numbers directly compromise internal user experience and IT team credibility. | Why this metric: Quantifies technical resolution speed; excessive operational resolution lag is the direct foundational cause of subsequent SLA violations. |

### 3️⃣ Ideate - Structuring the Dashboard

| | **Page 1: Helpdesk Overview** | **Page 2: Ticket Types & Categories** | **Page 3: Resolution & Spatial Analytics** |
|---|---|---|---|
| **Layer 0 (Scorecards)** | Macro Metrics: Total Ticket Volume, System MTTR, Overall % SLA Breach. | Total Volume by Type, Active Category Volumetric Counts. | Average Daily Ticket Ingestion, Average Resolution Days tracking cards. |
| **Layer 1 (1-dimension breakdown)** | Monthly incoming ticket volumetric trend line, Influx distributions split by Priority tier. | Top 5 Primary Ticket Classification Tags, Ticket SLA Breach volumes by Day of the Week. | Geographic spatial map visualizations detailing country volume, Queue Compliance comparison charts. |
| **Layer 2 (2-dimension breakdown)** | Workload growth rate matrices evaluated simultaneously across multiple ticket categories and timeline intervals. | Cross-segmentation matrices mapping ticket volume variants by category tags and priority tiers. | Two-dimensional Temporal Calendar Heatmaps (Day × Month), Queue Performance Matrix (MTTR vs SLA Breach), Type × Country MTTR performance grids. |

---

## ⚒️ Main Process

1️⃣ **Connect & Load Data** - Linked Power BI directly to the raw helpdesk source workbook, verified datatypes, and streamlined tracking records within the Power Query workspace.

2️⃣ **Data Modeling** - Normalized the single flat structure into a modular layout, executed column unpivoting to consolidate the 8 horizontal tag fields, established an intermediary bridge table, and constructed the active Star Schema schema.

3️⃣ **DAX Measures** - Formulated custom calculations to track cumulative ticket volume, evaluate exact network resolution time averages (MTTR), measure Month-over-Month volume variances, and calculate conditional SLA breach logic.

4️⃣ **Power BI Visualization** - Built the front-end reporting pages integrated with sliding bookmark panels, dynamic hover tooltips, and cross-report interactive drill-through tables.

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
