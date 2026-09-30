# AdTech OLAP Analytics Cube & Business Intelligence (SSAS & Power BI)

[![Platform](https://img.shields.io/badge/Platform-SSAS%20MOLAP-red.svg)](https://learn.microsoft.com/en-us/analysis-services/ssas-overview)
[![Analytics](https://img.shields.io/badge/Analytics-Excel%20%7C%20Power%20BI-yellow.svg)]()
[![Model](https://img.shields.io/badge/Model-Multidimensional%20Cube-blue.svg)]()
[![Operations](https://img.shields.io/badge/OLAP-Roll--up%20%7C%20Drill--down%20%7C%20Slice%20%7C%20Dice-green.svg)]()

The **Online Analytical Processing (OLAP)** and **Business Intelligence (BI)** semantic layer of the AdTech analytics platform. Built with **Microsoft SQL Server Analysis Services (SSAS)**, this multidimensional cube pre-aggregates billions of advertising touchpoints across platforms, campaigns, and audience demographics—delivering sub-second querying for **Excel Pivot Tables** and interactive **Power BI Dashboards**.

---

## Architectural Context

This project serves as the analytical consumption layer directly downstream of the `AdTech_DW` Data Warehouse:

```
[ AdTech_DW (Star Schema) ]
              │
              ▼
[ SSAS Data Source View: Ad Tech DW.dsv ]
              │
              ▼
[ AdTechCube Multidimensional Model (MOLAP) ]
 ├── Dimensions: User, Ad, Campaign, Date, Time
 └── Measure Groups: FactAdEvent (Counts, Spend, Revenue, Durations)
              │
       ┌──────┴────────────────────────┐
       ▼                               ▼
[ Excel OLAP Pivot Analysis ]    [ Power BI Service Dashboards ]
 - Roll-up / Drill-down           - Matrix Visual (Groupings)
 - Slice / Dice / Pivot           - Cascading Slicers & Dynamic KPIs
                                  - Hierarchical Drill-downs
                                  - Granular Drill-through Reports
```

---

## Multidimensional Cube Design

### Data Source & View
* **Data Source**: OLE DB connection to `AdTech_DW` on Microsoft SQL Server.
* **Data Source View ([Ad Tech DW.dsv](file:///c:/Users/pasin/Downloads/Database%20Project/AdTechCubeProject-master/Ad%20Tech%20DW.dsv))**: Defines foreign key relationships between `dbo.FactAdEvent` and all 5 dimension tables.

### Dimensions & Attribute Hierarchies
1. **`Dim Date`**:
   * **Attributes**: `DateKey`, `FullDate`, `DayName`, `MonthNumber`, `MonthName`, `QuarterNumber`, `YearNumber`, `IsWeekend`.
   * **Hierarchy**: `Calendar Hierarchy`: $\text{Year} \rightarrow \text{Quarter} \rightarrow \text{Month} \rightarrow \text{FullDate}$
2. **`Dim Time`**:
   * **Attributes**: `TimeKey`, `FullTime`, `HourNumber`, `MinuteNumber`, `SecondNumber`, `DayPart`.
   * **Hierarchy**: `Time of Day Hierarchy`: $\text{DayPart (Morning/Afternoon/Evening/Night)} \rightarrow \text{Hour}$
3. **`Dim User`**:
   * **Attributes**: `UserKey`, `UserNK`, `UserGender`, `UserAge`, `AgeGroup`, `Country`, `Location`, `PrimaryInterest`, `AllInterests`, `IsCurrent`.
   * Enables demographic segmentation (e.g., Country $\rightarrow$ Location $\rightarrow$ User Age Group).
4. **`Dim Ad`**:
   * **Attributes**: `AdKey`, `AdNK`, `AdPlatform` (Google Ads, Meta, YouTube, Display), `AdType` (Video, Carousel, Static), `TargetGender`, `TargetAgeGroup`.
5. **`Dim Campaign`**:
   * **Attributes**: `CampaignKey`, `CampaignNK`, `CampaignName`, `DurationDays`, `TotalBudget`.

### Measure Groups & Calculated Metrics
* **Base Measures** (Additive from `FactAdEvent`):
  * `Event Count`, `Impression Count`, `Click Count`
  * `Like Count`, `Comment Count`, `Share Count`, `Purchase Count`
  * `Allocated Spend`, `Revenue Generated`, `Transaction Process Time (Hours)`
* **Calculated Business KPIs**:
  * **Click-Through Rate (CTR)**: $\frac{\text{Click Count}}{\text{Impression Count}} \times 100\%$
  * **Conversion Rate (CVR)**: $\frac{\text{Purchase Count}}{\text{Click Count}} \times 100\%$
  * **Cost Per Click (CPC)**: $\frac{\text{Allocated Spend}}{\text{Click Count}}$
  * **Return on Ad Spend (ROAS)**: $\frac{\text{Revenue Generated}}{\text{Allocated Spend}}$

---

## OLAP Operations Demonstration (Microsoft Excel)

Connected via OLE DB / Analysis Services connection (`AdTechCubeProject`) to demonstrate core OLAP operations:
* **Roll-up**: Aggregating conversion revenue from daily event grain up to monthly and annual campaign totals.
* **Drill-down**: Expanding from `Quarter` down to `Month` and individual `Day` performance.
* **Slice**: Filtering metrics to inspect a single dimension member (e.g., analyzing metrics strictly for `AdPlatform = 'Meta'`).
* **Dice**: Subsetting the cube across multiple dimensions simultaneously (e.g., `Platform = 'Google'` AND `AgeGroup = '25-34'` AND `Year = 2025`).
* **Pivot**: Rotating axes (e.g., swapping Campaign rows and Month columns) to view conversion distributions from new analytical perspectives.

---

## Power BI Reporting Suite

Published to **Power BI Service**, featuring 4 dedicated analytical reports:

| Report | Purpose | Core Features & Design |
| :--- | :--- | :--- |
| **Report 1: Detailed Matrix Analysis** | Tabular deep-dive of marketing spend | Matrix visual displaying multi-level row groupings (Campaign $\rightarrow$ Ad Type) and column groupings (Year $\rightarrow$ Quarter) with conditional formatting on ROI. |
| **Report 2: Dynamic Cascading Dashboard** | Cross-platform audience insights | Cascading slicers where selecting an `AdPlatform` dynamically updates available `Campaign` options; linked to donut charts, bar visuals, and KPI cards. |
| **Report 3: Hierarchical Drill-down Report** | Time-series trend exploration | Interactive charts allowing drill-down along the Date hierarchy ($\text{Year} \rightarrow \text{Quarter} \rightarrow \text{Month}$) to pinpoint seasonal performance spikes. |
| **Report 4: Contextual Drill-through Report** | Root-cause ad performance diagnosis | Right-click navigation from high-level campaign summary visuals into granular ad-level creative metrics, user demographic breakdowns, and processing times. |

---

## Setup & Deployment Guide

1. Open [`AdTechCubeProject.sln`](file:///c:/Users/pasin/Downloads/Database%20Project/AdTechCubeProject-master/AdTechCubeProject.sln) in Visual Studio with SQL Server Data Tools (SSDT).
2. Update the connection string in [`AdTech_DW_DataSource.ds`](file:///c:/Users/pasin/Downloads/Database%20Project/AdTechCubeProject-master/AdTech_DW_DataSource.ds) to target your local `AdTech_DW` SQL Server database.
3. Configure the deployment target server (SSAS Multidimensional instance).
4. Right-click the project and choose **Deploy**.
5. Once deployed, right-click the database in SQL Server Management Studio (SSMS) or Visual Studio and choose **Process Database** (Full Process).
6. Connect Microsoft Excel or Power BI Desktop using **Get Data $\rightarrow$ Analysis Services**.
