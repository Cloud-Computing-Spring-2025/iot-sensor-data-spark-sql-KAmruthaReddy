### IoT Sensor Data Analysis with PySpark
This project analyzes IoT sensor data containing temperature and humidity readings across different building locations. The analysis is performed using PySpark and involves loading, exploring, filtering, aggregating, and analyzing time-series data through various transformations and SQL operations.

## Overview
The analysis consists of five main tasks, each focusing on different aspects of the sensor data:

Data loading and basic exploration
Data filtering and aggregation
Time-based analysis
Ranking using window functions
Pivot table analysis

## Input Data
The script expects a CSV file named sensor_data.csv in the current directory with the following columns:

sensor_id: Identifier for each sensor
timestamp: Date and time of the reading (format: yyyy-MM-dd HH:mm)
temperature: Temperature reading
humidity: Humidity reading
location: Physical location of the sensor
sensor_type: Type of sensor used

## Running the Code
```
spark-submit iot_sensor_analysis.py
```

## Tasks and Output Files
# Task 1: Load & Basic Exploration
This task involves loading the CSV data, creating a temporary view, and performing basic queries.

1. Loaded sensor_data.csv with schema inference
2. Created a temporary view named sensor_readings
3. Displayed first 5 rows, counted total records, and retrieved distinct locations and sensor types
4. Output saved to task1_output.csv

## output
```
+---------+-------------------+-----------+--------+----------------+-----------+
|sensor_id|          timestamp|temperature|humidity|        location|sensor_type|
+---------+-------------------+-----------+--------+----------------+-----------+
|     1096|2025-04-11 00:18:28|      23.74|   68.74|BuildingA_Floor1|      TypeA|
|     1030|2025-04-11 02:29:28|      18.27|   58.96|BuildingA_Floor2|      TypeA|
|     1043|2025-04-10 03:45:29|      29.46|   30.15|BuildingB_Floor2|      TypeA|
|     1036|2025-04-07 13:36:08|      20.56|   68.94|BuildingB_Floor2|      TypeB|
|     1049|2025-04-07 09:46:49|      27.47|   65.31|BuildingA_Floor2|      TypeA|
+---------+-------------------+-----------+--------+----------------+-----------+
```

# Task 2: Filtering & Simple Aggregations
This task analyzes temperature readings and aggregates data by location.

1. Filtered temperature readings to identify in-range (18-30°C) vs. out-of-range values
2. Grouped data by location and calculated average temperature and humidity
3. Sorted by average temperature to identify hottest locations
4. Output saved to task2_output.csv

## output
```
------ Aggregated Data by Location ------
+----------------+------------------+-----------------+
|        location|   avg_temperature|     avg_humidity|
+----------------+------------------+-----------------+
|BuildingA_Floor1|24.962286821705433|55.05523255813953|
|BuildingB_Floor1|24.911389961389972|55.92760617760617|
|BuildingB_Floor2|24.687456896551705| 54.4181034482759|
|BuildingA_Floor2|24.209362549800794|55.61430278884462|
+----------------+------------------+-----------------+
```

# Task 3: Time-Based Analysis
This task examines how temperature varies throughout the day.

1. Converted timestamp strings to timestamp type
2. Extracted hour of day (0-23) and grouped by hour
3. Calculated average temperature for each hour
4. Identified hour with highest average temperature
5. Output saved to task3_output.csv

# output
```
+-----------+------------------+
|hour_of_day|          avg_temp|
+-----------+------------------+
|          0|23.285897435897432|
|          1| 24.85153846153846|
|          2| 24.50976744186047|
|          3|26.468055555555555|
|          4|24.664181818181827|
|          5| 25.07927272727272|
|          6|24.763030303030295|
|          7|24.706666666666674|
|          8| 24.95333333333333|
|          9|25.122571428571433|
|         10|           24.4225|
|         11| 25.78113636363635|
|         12| 25.39029411764706|
|         13|             25.74|
|         14| 23.13687499999999|
|         15|25.371904761904766|
|         16| 24.24880952380953|
|         17|24.310952380952376|
|         18| 24.85783783783783|
|         19|23.321666666666665|
+-----------+------------------+
only showing top 20 rows

Hour with the highest average temperature:
+-----------+------------------+
|hour_of_day|          avg_temp|
+-----------+------------------+
|          3|26.468055555555555|
+-----------+------------------+
```

# Task 4: Window Function
This task ranks sensors based on their average temperature readings.

1. Computed average temperature for each sensor
2. Used RANK window function to order sensors by average temperature (descending)
3. Selected top 5 sensors with highest average temperatures
4. Output saved to task4_output.csv

# output
```
+---------+------------------+---------+
|sensor_id|          avg_temp|rank_temp|
+---------+------------------+---------+
|     1075|           31.9375|        1|
|     1068|             29.56|        2|
|     1004|29.113333333333337|        3|
|     1010|           29.0825|        4|
|     1057|             28.69|        5|
+---------+------------------+---------+
```

# Task 5: Pivot & Interpretation
This task creates a pivot table to identify patterns across locations and hours.

1. Created pivot table with location as rows and hour of day as columns
2. Calculated average temperature for each location-hour combination
3. Determined which (location, hour) had the highest average temperature
4. Output saved to task5_output.csv

# output
```
---------------------------- Hourly Temperature by Location -----------------------------
|     Location      |   0   |   1   |   2   |   3   |   4   |   5   |   6   |   7   |   8   |   9   |  10  |  11  |  12  |  13  |  14  |  15  |  16  |  17  |  18  |  19  |  20  |  21  |  22  |  23  |
|-------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|BuildingA_Floor1   |23.97 |24.73 |25.86 |23.59 |25.03 |27.79 |25.34 |22.97 |24.92 |26.84 |26.67 |26.91 |25.88 |25.42 |22.85 |27.87 |24.62 |24.27 |21.90 |24.22 |21.73 |25.64 |29.91 |25.47 |
|BuildingB_Floor2   |25.37 |23.19 |22.80 |27.32 |25.12 |23.44 |23.31 |28.86 |26.96 |23.13 |27.29 |24.66 |24.81 |28.24 |22.31 |24.05 |23.99 |25.20 |26.25 |24.35 |24.42 |21.29 |24.61 |21.59 |
|BuildingA_Floor2   |20.74 |24.14 |25.54 |25.10 |24.78 |23.87 |27.34 |24.22 |24.17 |27.01 |20.84 |27.14 |23.94 |25.00 |21.49 |26.40 |24.02 |22.96 |25.83 |22.43 |24.95 |20.69 |23.54 |21.73 |
|BuildingB_Floor1   |23.76 |27.14 |24.76 |29.07 |23.97 |25.75 |22.15 |22.41 |25.27 |24.13 |24.21 |24.87 |26.79 |23.92 |26.21 |23.75 |24.46 |24.81 |26.85 |21.67 |25.99 |26.28 |23.13 |26.09 |


------ (Location, Hour) with Highest Average Temperature ------
+----------------+-----------+------------------+
|        location|hour_of_day|          avg_temp|
+----------------+-----------+------------------+
|BuildingA_Floor1|         22|29.914285714285715|
+----------------+-----------+------------------+
```

## Conclusion
This analysis demonstrates several key Spark SQL capabilities for IoT data processing:

- Data loading and exploration
- Filtering and aggregation
- Time-based analysis using timestamp functions
- Window functions for ranking
- Pivot tables for multidimensional analysis
The findings reveal temperature and humidity patterns across different building locations and times of day, which could inform building management systems and energy optimization strategies.