**Language:** English | [Bahasa Indonesia](README.id.md)

# Transjakarta Ridership & Revenue Dashboard (Tableau)

An interactive Tableau dashboard that explores ridership, revenue, peak hours, busiest corridors and stops, and passenger profile using a **simulated** Transjakarta tap-in/tap-out dataset (April 2023).

**Live dashboard:** [Open on Tableau Public](https://public.tableau.com/app/profile/alnanda.kusuma/viz/TransjakartaRidershipRevenueDashboard/RidershipOverview)

## Dashboard Preview

**1. Ridership Overview**
![Ridership Overview](screenshots/01-overview.png)

**2. Stops & Passengers**
![Stops and Passengers](screenshots/02-stops-passengers.png)

## Business Questions
1. How many trips and how much revenue are there, and how do trips change day by day?
2. When are the peak hours (weekday vs weekend)?
3. Which corridors and boarding stops are the busiest, and where are they on the map?
4. What does the passenger profile look like (age group, gender, card bank)?
5. How complete is the data (missing tap-outs, duration)?

## Dataset
- **Source:** [Transjakarta Transportation Transaction](https://www.kaggle.com/datasets/dikisahkan/transjakarta-transportation-transaction) on Kaggle (file with about 180K rows; 189,500 rows loaded)
- **Important:** the transaction data is **simulated** (generated with Faker). Stop and corridor master data comes from Transjakarta's public GTFS feed. Findings describe patterns in the simulated data, not real Transjakarta operations.
- The raw CSV is not included in this repository.

## Key Metrics
| Metric | Value |
|---|---|
| Total trips | 189,500 |
| Total revenue | Rp496.6M (about Rp2,620 per trip) |
| Average trip duration | 72.1 minutes |
| Trips with missing tap-out | 3.5% |

## Workflow
1. **Data connection:** loaded the CSV in Tableau Public and fixed the locale so decimal values (coordinates, fares) were read as numbers.
2. **Data preparation:** renamed fields, set data types, and hid the card name and card ID fields so personal-looking identifiers are not included in the published extract.
3. **Calculated fields:** trip duration (minutes), hour of day, weekday, day type, age and age group, trips (distinct count), missing tap-out percentage, average fare per trip.
4. **Worksheets:** four KPI cards, daily trend, hour-by-weekday heatmap, top 10 corridors, stops map, top 10 boarding stops, age and gender, card bank share.
5. **Dashboards:** two dashboards with fixed size, filter actions, and navigation buttons.

## Key Calculated Fields
```
Trip Duration (min) =
IF NOT ISNULL([Tap Out Time]) AND [Tap Out Time] >= [Tap In Time]
THEN DATEDIFF('minute',[Tap In Time],[Tap Out Time]) END

Hour of Day = DATEPART('hour',[Tap In Time])

Day Type =
IF DATEPART('weekday',[Tap In Time]) = 1 OR DATEPART('weekday',[Tap In Time]) = 7
THEN 'Weekend' ELSE 'Weekday' END

Age Group =
IF [Age] < 20 THEN '<20' ELSEIF [Age] < 30 THEN '20-29'
ELSEIF [Age] < 40 THEN '30-39' ELSEIF [Age] < 50 THEN '40-49'
ELSEIF [Age] < 60 THEN '50-59' ELSE '60+' END

Trips = COUNTD([Trans ID])
Missing Tap-Out % = SUM(IF ISNULL([Tap Out Time]) THEN 1 ELSE 0 END) / COUNT([Trans ID])
```

## Key Findings
- **Weekly pattern:** about 8,000-10,000 trips per weekday vs about 2,000 on weekends. The first two days of April are almost empty because the data effectively starts on 3 April.
- **Peak hours (weekdays):** a morning peak around 06:00 (about 5.6K boardings) and an evening peak around 17:00 (about 5.2K).
- **Corridors:** ridership is spread across many corridors; the busiest has about 1.9K trips (roughly 1% of all trips).
- **Stops:** Penjaringan is the busiest boarding stop (about 1.4K), well above the next one.
- **Passengers:** the 30-39 age group has the most trips, and 60+ the fewest. Female passengers account for a larger share of trips than male passengers in every age group.
- **Card bank:** DKI cards account for about half of all trips, followed by e-money.

## Limitations
- The data is simulated and covers one month, so results are for practice and not for operational decisions.
- Weekday midday (about 10:00-15:00) is much quieter than weekends, which is unusual for real public transport and is probably an artifact of how the data was generated.
- Age is computed using 2023 as the reference year.
- Best viewed on desktop (fixed 1200 × 900 layout).

## Tools
Tableau Public

## Author
Alnanda www.linkedin.com/in/alnandakusuma · https://github.com/alnandakusuma