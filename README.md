# 📍 West Midlands Infrastructure & Traffic Audit (PostgreSQL)

![Complex Risk Ranker](complex_risk_ranker.png)
*(Above: A high-density audit using Window Functions to rank road stress by borough across 5.1 million records.)*

## 🎯 Project Overview
As a Civil Engineer based in Tipton, I wanted to investigate the actual pressure our local road network faces. I engineered a relational database in PostgreSQL to handle a national dataset of **5,113,740 rows** of traffic data.

**The Goal:** Move beyond Excel limitations to identify HGV "hotspots" and peak-hour bottlenecks specifically in Birmingham, Sandwell, and the wider West Midlands.

---

## 💡 What the Data Shows
*Summary: Extracting local engineering insights from national data.*

### 1. Regional Traffic Leaderboard
I aggregated the total traffic volume to see which local authorities handle the highest load. This helps in regional budget allocation for road improvements.
![Regional Leaderboard](simple_regional_load.png)

### 2. HGV Impact (Structural Fatigue)
In Birmingham, I found that Heavy Goods Vehicles (HGVs) account for over **15% of traffic** on major A-roads. Since lorries cause significantly more wear than cars, this identifies where resurfacing is most urgent.
![Birmingham HGV Impact](medium_hgv_impact.png)

### 3. Local Deep-Dive: Sandwell Active Travel
I zoomed into my home borough to identify the top roads for cyclists. This data can be used to justify the placement of new cycle lanes in Tipton and Wednesbury.
![Sandwell Cycling Audit](sandwell_active_travel.png)

---

## 🛠️ The Technical Engine
I architected a "Star-Schema" database to link geographic sensor locations to millions of traffic observations.

### 1. Handling "Enterprise-Scale" Data
The database successfully manages over 5 million rows, providing a level of detail that standard spreadsheets cannot process.
![Database Scale Audit](database_scale.png)

### 2. Solving ETL Roadblocks (Dirty Data)
The import initially failed due to "NULL" strings in the source file. I resolved this by re-configuring the **ETL (Extract, Transform, Load)** parameters in pgAdmin to ensure 100% data integrity.
![ETL Process Logs](etl_process_logs.png)

### 3. Advanced SQL Logic
I utilized **Common Table Expressions (CTEs)** and **Window Functions (`RANK() OVER`)** to create a borough-by-borough risk leaderboard.

```sql
-- Ranking infrastructure stress within each West Midlands borough
WITH RoadStress AS (
    SELECT 
        cp.local_authority_name, cp.road_name,
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
ORDER BY total_hgvs DESC;
