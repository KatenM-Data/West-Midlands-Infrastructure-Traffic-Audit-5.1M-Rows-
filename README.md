# 🛣️ West Midlands Regional Infrastructure & Traffic Audit (PostgreSQL)

![Complex Risk Ranker](complex_risk_ranker.png)
*(Above: Utilising Window Functions to rank infrastructure stress across local authorities within a 5.1-million-row database.)*

## 🎯 The Objective
As a Tipton-based BEng Civil Engineer, I wanted to investigate the actual pressure our local road network faces, treating it as a physical supply chain. I engineered a relational database in PostgreSQL to handle a national dataset from the Department for Transport (DfT) containing **5,113,740 rows** of traffic observations.

**The Goal:** Move beyond spreadsheet limitations to identify Heavy Goods Vehicle (HGV) "hotspots", peak-hour bottlenecks, and active travel trends specifically within the West Midlands.

---

## 💡 What the Data Shows
*Summary: Extracting local engineering insights from national data.*

### 1. Structural Fatigue (HGV Impact on A-Roads)
In Birmingham, I found that HGVs account for nearly **8% of all traffic** on major Principal A-Roads like the **A47** and **A45**. Because a single lorry causes exponentially more structural wear than a car (Fourth Power Law), this analysis identifies exactly which single-carriage routes require prioritised resurfacing and inspection.
![Birmingham HGV Impact](medium_hgv_impact.png)

### 2. Network Pressure (Peak Hours)
The network hits its absolute limit at **17:00 (5 PM)** and **08:00 (8 AM)**. By isolating these bimodal rush-hour peaks across the region, logistics firms can implement "shift-staggering" to reduce fleet idling times and avoid the heaviest congestion.
![Peak Hour Analysis](peak_hour_analysis.png)

### 3. Regional Load & Active Travel
*   **Regional Traffic:** Warwickshire, Staffordshire, and Birmingham handle the highest overall traffic volumes, driving regional budget allocations.
![Regional Leaderboard](simple_regional_load.png)
*   **Sandwell Cycling Deep-Dive:** I isolated the top routes for pedal cycles in my home borough of Sandwell (such as the A457). This provides data-validated evidence to justify local "Green Infrastructure" and cycle-lane funding.
![Sandwell Cycling Audit](sandwell_active_travel.png)

---

## 🛠️ The Technical Engine (PostgreSQL)
I architected a relational database using a **Star-Schema** approach to link geographic sensor metadata to millions of traffic observations, ensuring sub-second query performance.

### 1. Database Scale & Integrity
The database successfully manages over 5.1 million observations linked to 46,000 sensor locations across the UK.
![Database Scale](database_scale.png)

### 2. Solving ETL Roadblocks (Dirty Data)
During the bulk-load phase, the import failed because the raw CSV contained "NULL" as a text string inside numeric columns. I resolved this by re-configuring the **ETL (Extract, Transform, Load)** null-mapping parameters in pgAdmin, ensuring 100% data integrity for the final load.
![ETL Process Logs](etl_process_logs.png)

### 3. Advanced SQL Logic
I developed a suite of queries to demonstrate full SQL proficiency, ranging from simple aggregations to complex Window Functions.

```sql
-- Complex Query: Ranking infrastructure stress within each West Midlands borough
WITH RoadStress AS (
    SELECT 
        cp.local_authority_name,
        cp.road_name,
        SUM(rc.all_hgvs) as total_hgvs,
        SUM(rc.all_motor_vehicles) as total_vol
    FROM raw_counts rc
    JOIN count_points cp ON rc.count_point_id = cp.count_point_id
    WHERE cp.region_name = 'West Midlands'
    GROUP BY 1, 2
)
SELECT *,
       RANK() OVER(PARTITION BY local_authority_name ORDER BY total_hgvs DESC) as stress_rank
FROM RoadStress
WHERE total_vol > 100000
ORDER BY total_hgvs DESC
LIMIT 15;
```
## 🏆 Skills Demonstrated
*   **Database Management:** Built a local PostgreSQL database from scratch to handle a dataset of **5.1 Million rows** that was too large for Excel.
*   **SQL Logic:** Used intermediate/advanced SQL (CTEs, Window Functions, and Joins) to filter, group, and rank large amounts of traffic data.
*   **AI Prompting:** Used AI tools to help write SQL syntax faster, troubleshoot error codes, and speed up the coding process.
*   **Data Cleaning (ETL):** Troubleshooted import errors and fixed "dirty" data (like text strings appearing in number columns) so the database would load correctly.
*   **Engineering Context:** Used my Civil Engineering background to relate heavy traffic numbers (HGV volume) to physical road wear-and-tear.

---

### 🎓 Project Credentials
*   **Developer:** Katen Morker
*   **Credentials:** BEng (Hons) Civil Engineering | Certified Data Analyst
*   **Source Data:** UK Department for Transport (DfT) Count Point Datasets
