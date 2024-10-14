# cyclistic-case-study
This repository contains a data analysis case study on Cyclistic's bike-sharing program, examining trip data from 2019 and 2020. The project includes SQL queries for data cleaning, exploration, and aggregation, as well as data visualizations built in Google Data Studio.
Cyclistic Bike-Share Data Analysis Case Study
Overview
This project is an exploratory data analysis case study on Cyclistic's bike-sharing program, focusing on trip data from the years 2019 and 2020. The objective was to understand patterns in bike usage, differentiate between user types, and uncover insights that can inform business decisions. Key findings from the analysis include usage patterns across customer types, trip durations, and weekly trends. This project demonstrates SQL skills for data cleaning, transformation, and aggregation, with visualizations created in Google Data Studio for deeper insight into the data.

Dataset
The datasets used in this analysis consist of bike-share trip data for 2019 and 2020. The two tables, divvy_trips_2019 and divvy_trips_2020, were merged into a combined dataset for analysis.

Key Columns:
2019 Table: trip_id, start_time, end_time, from_station_name, to_station_name, usertype
2020 Table: ride_id, started_at, ended_at, start_station_name, end_station_name, member_casual
SQL Queries and Analysis
The analysis is divided into various segments:

1. Data Exploration and Cleaning
Initial queries count the total rows in each dataset and explore distinct entries.
Column names are checked to ensure consistency across the two datasets.
2. Data Merging
A new table, all_trips_2019_2020, was created by combining the 2019 and 2020 data using a UNION ALL statement.
Data types were standardized across the two datasets to allow for a seamless merge.
3. Aggregate Calculations
Average trip duration, maximum, and minimum trip duration for 2019 were calculated.
Grouped analysis by user type (2019) and by day of the week to understand behavioral patterns.
A summary report table (summary_report) was created to consolidate all key findings into one location for easier reporting.
4. Creating the Summary Report Table
This table aggregates the total ride count and average duration for each year.
Summarizes trip data by user type and by day of the week.
Visualization
The results were visualized using Google Data Studio to highlight key patterns:

Bar Charts: Illustrate total rides by year and user type.
Line Charts: Show trends in trip duration over time.
Tables: Summarize average trip durations and ride counts.
SELECT COUNT(*) AS total_rows
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`;

SELECT COUNT(*) AS total_rows
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020`;

SELECT DISTINCT *
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`;

SELECT DISTINCT *
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020`;

SELECT 
    MAX(tripduration) AS max_value,
    MIN(tripduration) AS min_value,
    AVG(tripduration) AS mean_value
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`;

-- Check column names for 2019 table
SELECT * FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019` LIMIT 1;

-- Check column names for 2020 table
SELECT * FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020` LIMIT 1;

-- Check columns for the 2019 table
SELECT * FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019` LIMIT 1;

-- Check columns for the 2020 table
SELECT * FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020` LIMIT 1;



CREATE OR REPLACE TABLE `aqueous-cabinet-400300.bicycle_trips.all_trips_2019_2020` AS
SELECT 
    CAST(trip_id AS STRING) AS trip_id,
    start_time,                          
    end_time, 
    from_station_name AS start_station_name, 
    from_station_id AS start_station_id,
    to_station_name AS end_station_name,      
    to_station_id AS end_station_id, 
    usertype AS rider_type,               
    NULL AS rideable_type                 
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`
UNION ALL
SELECT 
    CAST(ride_id AS STRING) AS trip_id,  -- Replace with actual column name
    started_at AS start_time,
    ended_at AS end_time,
    start_station_name AS from_station_name,  
    start_station_id AS from_station_id,
    end_station_name AS to_station_name,      
    end_station_id AS to_station_id, 
    rideable_type AS usertype,   
    rideable_type
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020`;

SELECT usertype, AVG(tripduration) AS avg_duration
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`
GROUP BY usertype;

SELECT start_time AS day_of_week, COUNT(*) AS ride_count
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`
GROUP BY day_of_week
ORDER BY day_of_week;




CREATE OR REPLACE TABLE `aqueous-cabinet-400300.bicycle_trips.summary_report` AS

-- Total number of rides and average duration for 2019
SELECT 
    '2019' AS year,
    'overall' AS category,
    COUNT(*) AS ride_count,
    AVG(tripduration) AS avg_trip_duration,
    NULL AS user_type,         -- Placeholder for consistency
    NULL AS day_of_week        -- Placeholder for consistency
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`

UNION ALL

-- Total number of rides and average duration for 2020
SELECT 
    '2020' AS year,
    'overall' AS category,
    COUNT(*) AS ride_count,
    AVG(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) AS avg_trip_duration,
    NULL AS user_type,         -- Placeholder for consistency
    NULL AS day_of_week        -- Placeholder for consistency
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020`

UNION ALL

-- Average trip duration by user type for 2019
SELECT 
    '2019' AS year,
    'by_user_type' AS category,
    COUNT(*) AS ride_count,
    AVG(tripduration) AS avg_trip_duration,
    usertype AS user_type, 
    NULL AS day_of_week        -- Placeholder for consistency
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`
GROUP BY user_type

UNION ALL

-- Average trip duration by user type for 2020
SELECT 
    '2020' AS year,
    'by_user_type' AS category,
    COUNT(*) AS ride_count,
    AVG(TIMESTAMP_DIFF(ended_at, started_at, SECOND)) AS avg_trip_duration,
    member_casual AS user_type, 
    NULL AS day_of_week        -- Placeholder for consistency
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2020`
GROUP BY user_type

UNION ALL

-- Rides by day of the week for 2019
SELECT 
    '2019' AS year,
    'by_day_of_week' AS category,
    COUNT(*) AS ride_count,
    NULL AS avg_trip_duration, -- Placeholder for consistency
    NULL AS user_type,         -- Placeholder for consistency
    FORMAT_TIMESTAMP('%A', start_time) AS day_of_week
FROM `aqueous-cabinet-400300.bicycle_trips.divvy_trips_2019`
GROUP BY day_of_week

ORDER BY year, category;




