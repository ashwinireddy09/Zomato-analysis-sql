# Zomato-analysis-sql
Zomato Data Analysis project using MySQL to explore restaurant trends across cities and countries. Includes analysis of ratings, pricing, restaurant openings, and cost conversion to USD. Designed to extract meaningful business insights from real-world data.


Project Overview

- Project Title: Zomato Restaurant Analysis
- Level: Beginner to Intermediate
- Database: zomato_analysis_p1

This project is designed to demonstrate SQL skills and techniques used by data analysts to explore, clean, and analyze real-world restaurant data from Zomato. The project involves setting up a Zomato database, performing data cleaning, exploratory data analysis (EDA), and answering key business questions using SQL. This project is ideal for beginners who want hands-on practice with SQL using a real business dataset.


## Objectives

- Set up the Zomato restaurant database.
- Perform data cleaning and handle missing values.
- Conduct exploratory data analysis (EDA).
- Analyze restaurant trends, pricing, ratings, and customer preferences.
- Derive meaningful business insights using SQL queries.


## Project Structure

- **Database Creation**: The project starts by creating a database named `Zomato_analysis_p1`.
- **Table Creation**: A table named `Zomato_analysis` is created to store restaurant-related data. The table structure includes columns for restaurant ID, restaurant name, country, city, cuisine type, average cost for two, currency, rating, votes, online delivery availability, price range, and opening date.
 
```sql

CREATE TABLE Zomato_analysis (
    restaurant_id INT PRIMARY KEY,
    restaurant_name VARCHAR(100),
    country VARCHAR(50),
    city VARCHAR(50),
    locality VARCHAR(100),
    cuisines VARCHAR(150),
    currency VARCHAR(10),
    average_cost_for_two INT,
    rating FLOAT,
    votes INT,
    has_online_delivery VARCHAR(5),
    is_delivering_now VARCHAR(5),
    price_range INT,
    opening_date DATE
);

```

# 2. Data Exploration & Cleaning
- Record Count : Determine the total number of restaurant records in the dataset.
- Unique Restaurant Count: Find how many unique restaurants are in the dataset.
- Category (Cuisines) Count: Identify all unique cuisines in the dataset.
- Unique City Count: Find how many unique cities are covered.
- Null Value Check: Check for missing or NULL values in important columns.
- Delete Records with Missing Data: Remove all rows that contain NULL values.**

```sql
SELECT COUNT(*) FROM zomato_restaurants;

SELECT COUNT(DISTINCT city) FROM zomato_restaurants;

SELECT DISTINCT cuisines FROM zomato_restaurants;

SELECT * 
FROM zomato_restaurants
WHERE 
    restaurant_name IS NULL OR city IS NULL OR country IS NULL OR
    average_cost_for_two IS NULL OR rating IS NULL;


DELETE FROM zomato_restaurants
WHERE 
    restaurant_name IS NULL OR city IS NULL OR country IS NULL OR
    average_cost_for_two IS NULL OR rating IS NULL;

```

# 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. Convert the Average cost for 2 column into USD dollars (currently the Average cost for 2 in local currencies:
```sql
SELECT 
    RestaurantID,
    City,
    CountryCode,
    Average_Cost_for_two,
    ROUND(Average_Cost_for_two / 83, 2) AS Average_Cost_in_USD
FROM zomato_analysis;
```

2. Find the Numbers of Resturants based on City and Country.:
```sql

SELECT 
    CountryCode,
    City,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY CountryCode, City
ORDER BY Total_Restaurants DESC;

```

3. Numbers of Resturants opening based on Year, Quarter, Month:
```sql

SELECT 
    YEAR(Datekey_Opening) AS Opening_Year,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY Opening_Year
ORDER BY Opening_Year;

SELECT 
    YEAR(Datekey_Opening) AS Opening_Year,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY Opening_Year
ORDER BY Opening_Year;

SELECT 
    YEAR(Datekey_Opening) AS Year,
    QUARTER(Datekey_Opening) AS Quarter,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY Year, Quarter
ORDER BY Year, Quarter;



```

4. Count of Resturants based on Average Ratings:
```sql
YEAR(Datekey_Opening) AS Year,
    MONTH(Datekey_Opening) AS Month,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY Year, Month
ORDER BY Year, Month;

```

5. Create buckets based on Average Price of reasonable size and find out how many resturants falls in each buckets:

```sql
SELECT 
    CASE 
        WHEN Average_Cost_for_two < 300 THEN 'Low Budget (<300)'
        WHEN Average_Cost_for_two BETWEEN 300 AND 700 THEN 'Mid Range (300-700)'
        WHEN Average_Cost_for_two BETWEEN 701 AND 1500 THEN 'Premium (701-1500)'
        ELSE 'Luxury (>1500)'
    END AS Price_Bucket,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY Price_Bucket
ORDER BY Total_Restaurants DESC;

```

6. Percentage of Resturants based on "Has_Table_booking":
```sql
SELECT 
    Has_Table_booking,
    COUNT(*) AS Total_Restaurants,
    ROUND(
        COUNT(*) * 100.0 / (SELECT COUNT(*) FROM zomato_analysis),
        2
    ) AS Percentage
FROM zomato_analysis
GROUP BY Has_Table_booking;

```

7. Percentage of Resturants based on "Has_Online_delivery":
```sql
SELECT 
    Has_Online_delivery,
    COUNT(*) AS Total_Restaurants,
    ROUND(
        COUNT(*) * 100.0 / (SELECT COUNT(*) FROM zomato_analysis),
        2
    ) AS Percentage
FROM zomato_analysis
GROUP BY Has_Online_delivery;

```
8. Develop Charts based on Cusines, City, Ratings (Candidate have to think about new KPI to analyse):
```sql
SELECT 
    Has_Online_delivery,
    COUNT(*) AS Total_Restaurants,
    ROUND(
        COUNT(*) * 100.0 / (SELECT COUNT(*) FROM zomato_analysis),
        2
    ) AS Percentage
FROM zomato_analysis
GROUP BY Has_Online_delivery;

SELECT 
    City,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY City
ORDER BY Total_Restaurants DESC;

SELECT 
    Rating,
    COUNT(RestaurantID) AS Total_Restaurants
FROM zomato_analysis
GROUP BY Rating
ORDER BY Rating DESC;


```
9. Build a Dashboard for the KPI's Above.:

```sql
SELECT  
    COUNT(RestaurantID) AS Total_Restaurants,
    ROUND(AVG(Rating),2) AS Avg_Rating,
    ROUND(AVG(Average_Cost_for_two),2) AS Avg_Cost_For_Two,
    SUM(CASE WHEN Has_Table_booking = 'Yes' THEN 1 ELSE 0 END) AS Table_Booking_Restaurants,
    SUM(CASE WHEN Has_Online_delivery = 'Yes' THEN 1 ELSE 0 END) AS Online_Delivery_Restaurants
FROM zomato_analysis;

```
## Findings

- Customer Preferences: Majority of restaurants fall under the mid-range cost category.

- Premium Dining: High-value restaurants are concentrated in metro cities.

- Top Cuisines: North Indian, Chinese, and Fast Food dominate the market.

- Online Delivery Trend: A significant percentage of restaurants support online delivery.

- Popular Cities: Cities like Bangalore, Delhi, and Mumbai lead in restaurant count and ratings.

## Reports

- Restaurant Performance Report: Based on ratings, cost, and delivery availability.

- City-wise Insights: Restaurant distribution and pricing trends.

- Cuisine Analysis: Popular cuisines and their average ratings.

- Pricing Strategy: Budget vs Mid-range vs Premium segmentation.

## Conclusion

This Zomato Restaurant Analysis SQL Project provides a complete hands-on learning experience in SQL, covering database setup, data cleaning, exploratory data analysis, and business-driven queries. The insights derived from this project help understand restaurant pricing, customer preferences, city-wise restaurant performance, and online delivery trends—making it highly relevant for real-world data analyst roles.



