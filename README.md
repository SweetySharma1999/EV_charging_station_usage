# EV_charging_station_usage


https://colab.research.google.com/drive/14AUxoaFxNULC_vD3SUHr4tRjVthky-KU



# EV Charging Station Usage & Grid Load Analysis Dashboard

An interactive Power BI data analytics solution designed to monitor Electric Vehicle (EV) infrastructure demand, optimize energy dispatch strategies, and predict grid instability risk. This repository maps the business requirements, analytical scopes, data preparation steps, and DAX calculations needed to build the operational dashboard.

---

## 1. Project Scope & Architecture

### Problem Statement
As Electric Vehicle adoption grows exponentially, localized charging clusters create unpredictable demand spikes on the utility grid. System operators require an operational view to balance utility loads, measure green energy penetration, and flag potential peak overload thresholds before they cascade into local failures.

### Target Objectives
* **Infrastructure Utilization:** Track charging frequency and hardware processing speeds across major urban zones.
* **Grid Load Balance:** Isolate grid-stress intervals (measured in Megawatts) driven by high concurrent vehicle loads.
* **Sustainability Performance:** Audit the exact percentage of renewable energy supporting various station frameworks.
* **Risk Mitigation:** Filter and alert structural peak load risk periods (Low, Medium, High).

### Data Schema Overview
The pipeline models a single history log table containing the following elements:
* `record_id`: Chronological unique session sequence key.
* `date_time`: Timestamps mapped hourly across operational dates.
* `city_zone`: Geographic sector labels (North, South, East, West, Central).
* `station_type`: Hardware capability grouping (Normal, Fast, Supercharger).
* `vehicles_charged`: Live session concurrent vehicle tracking.
* `avg_charging_duration_minutes`: Average processing time duration per battery connection.
* `energy_dispensed_kwh`: Volumetric electrical power delivery logs.
* `grid_load_mw`: Instantaneous total power draw applied to local transformers.
* `renewable_energy_used_percent`: Relative clean energy distribution percentage.
* `peak_load_risk`: Categorical system risk status classification.

---

## 2. Data Transformation Layer (Power Query)

### File Name: `Get_EV_Data.m`
*Directions: In Power BI Desktop, navigate to **Transform Data** -> **New Source** -> **Blank Query** -> **Advanced Editor** and drop the code below.*

```powerquery
let
    // Replace the directory file path below with your local folder path configuration
    Source = Csv.Document(File.Contents("C:\YourFolderPath\ev_charging_station_usage_grid_load.csv"), [Delimiter=",", Columns=10, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    PromotedHeaders = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    
    // Explicitly enforce system data types matching original Pandas DataFrame schema
    ChangedTypes = Table.TransformColumnTypes(PromotedHeaders, {
        {"record_id", Int64.Type}, 
        {"date_time", type datetime}, 
        {"city_zone", type text}, 
        {"station_type", type text}, 
        {"vehicles_charged", Int64.Type}, 
        {"avg_charging_duration_minutes", type number}, 
        {"energy_dispensed_kwh", type number}, 
        {"grid_load_mw", type number}, 
        {"renewable_energy_used_percent", type number}, 
        {"peak_load_risk", type text}
    })
in
    ChangedTypes
