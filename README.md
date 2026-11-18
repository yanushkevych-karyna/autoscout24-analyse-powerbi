# AutoScout24 Car Listings – Power BI Dashboard
 
🇩🇪 [Deutsche Version]()
 
## Overview
 
This project is my first Power BI dashboard. It analyses more than 116,000 car listings from AutoScout24 to understand how the market is structured and which factors influence the asking price (mileage, age, brand, fuel type, seller type and more).
 
The goal was to practise a complete analytics workflow: importing raw data, cleaning it in Power Query, building measures with DAX and presenting the results in an interactive dashboard.
 
## Dashboard
 

 

 
## Dataset
 
- **Source:** [AutoScout24 Car Listings Dataset on Kaggle](https://www.kaggle.com/datasets/clkmuhammed/autoscout24-car-listings-dataset)
- **Size:** 118,382 listings, 75 columns (raw data)
- **Content:** price, make and model, mileage, registration date, fuel type, power, transmission, seller type, location, accident and service history, equipment and more
## Business Questions
 

## Data Cleaning (Power Query)
 
| Step | Rows remaining |
|---|---|
| Raw data | 118,382 |
| Removed listings without registration date | 118,161 |
| Kept registrations from 01.01.2000 onwards | 118,047 |
| Kept engine power between 30 and 1,500 hp | 118,008 |
| Removed prices below 500 EUR | 117,949 |
| Removed prices above 300,000 EUR and mileage above 500,000 km | 117,246 |
| Kept passenger cars only (removed vans) | 116,792 |
 
**Decisions and reasoning**
 
- **Removed columns:** 26 columns were dropped because they were almost or completely empty (e.g. warranty, fuel consumption in city and highway, VIN) or not relevant for the analysis (e.g. description, street, equipment lists).
- **Missing values:** Missing values were only removed for the registration date, because it is needed to calculate the vehicle age. Missing engine power and mileage were kept so that the listings could still be used for other analyses.
- **Outliers:** Classic and collector cars (registered before 2000) and extreme price, power and mileage values were excluded, because they distort averages. For example, one BMW 316d was listed for 9.5 million EUR, which is clearly an error. Very expensive supercars were excluded on purpose, since this project focuses on the regular car market.
- **Import issue:** Decimal values such as `22950.0` were first read ten times too large (`229500`) because of German regional settings, where the dot is a thousands separator. This was fixed by setting the import locale to English (United States).
- **Mileage:** The column contained text such as `10,236 km`. The unit was removed and the column was converted to a whole number.
- **Currency:** All prices are in EUR.
**New columns**
 
- `car_age`: age of the car in years, calculated from the registration date (reference year 2026)
- `price_group`: price brackets (under 10k, 10k–20k, 20k–40k, 40k–80k, 80k+)
## Data Model and DAX Measures
 

 
## Dashboard Pages

 
## Key Findings
 

## Limitations
 
- The data shows **asking prices** of listings, not actual sales prices.
- The data is a snapshot of one point in time.
- Filter thresholds (year, price, mileage, power) are my own analytical decisions and affect the results.
## Tools
 
Power BI Desktop, Power Query, DAX
