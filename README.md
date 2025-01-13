# Netfilx Movies and TV Shows Data Analysis using SQL

![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRRS4lg5fcLPbmReg1LQjegziOyYiWmBEMfVg&s)
## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.
## Objectives
- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.
## Dataset
The data for this project is sourced from the Kaggle dataset:
- Dataset Link: [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)
## Business Problems and Solutions 
### 1. Count the Number of Movies vs TV Shows
~~~sql
SELECT type,COUNT(*) as total_content
FROM netflix
GROUP BY type;;
~~~
**Objective:** Determine the distribution of content types on Netflix.
### 2. Find the Most Common Rating for Movies and TV Shows
~~~sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
~~~

  
  
  


