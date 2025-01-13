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
### 3. List All Movies Released in a Specific Year (e.g., 2020)
~~~ sql
SELECT * 
FROM netflix
WHERE type ='movie'
and
release_year = 2020;
~~~
### 4. Find the Top 5 Countries with the Most Content on Netflix
~~~ sql
SELECT TOP 5 
    value AS new_country,
    COUNT(show_id) AS total_content
FROM netflix
CROSS APPLY STRING_SPLIT(country, ',')
GROUP BY value
ORDER BY total_content DESC;
~~~
### 5. Identify the Longest Movie
~~~ sql
select * from netflix where type='movie'
and duration=(select max(duration) from netflix);
~~~
### 6 Find Content Added in the Last 5 Years
~~~ sql
SELECT *
FROM netflix
WHERE CAST(date_added AS DATE) >= DATEADD(YEAR, -5, GETDATE());
~~~
### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
~~~ sql
SELECT *
FROM netflix where director like'%rajiv chilaka%';
~~~
### 8. List All TV Shows with More Than 5 Seasons
~~~ sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND CAST(LEFT(duration, CHARINDEX(' ', duration) - 1) AS INT) > 5;
~~~
### 9. Count the Number of Content Items in Each Genre
~~~ sql
SELECT 
    value AS genre,
    COUNT(show_id) AS total_content
FROM netflix
CROSS APPLY STRING_SPLIT(listed_in, ',')
GROUP BY value;
~~~
### 10.Find each year and the average numbers of content release in India on netflix.
~~~ sql
select extract(year from to_date(date_added,'month dd,yyyy')) as year,
count(*) as yearly_content,
round(
count(*)::numeric/select count(*) from netflix where country = 'india')::numeric *
100,2)as avg_content_per_year
from netflix
where country = 'india'
group by 1;
SELECT 
    YEAR(CONVERT(DATE, date_added, 103)) AS year,
    COUNT(*) AS yearly_content,
    ROUND(
        (COUNT(*) * 1.0 / (SELECT COUNT(*) FROM netflix WHERE country = 'india')) * 100, 2
    ) AS avg_content_per_year
FROM netflix
WHERE country = 'india'
GROUP BY YEAR(CONVERT(DATE, date_added, 103));
SELECT 
    YEAR(PARSE(date_added AS DATETIME USING 'en-US')) AS year,
    COUNT(*) AS yearly_content,
    ROUND(
        (COUNT(*) * 1.0 / (SELECT COUNT(*) FROM netflix WHERE country = 'india')) * 100, 2
    ) AS avg_content_per_year
FROM netflix
WHERE country = 'india'
GROUP BY YEAR(PARSE(date_added AS DATETIME USING 'en-US'));
~~~
### 11. List All Movies that are Documentaries
~~~ sql
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
~~~
### 12. Find All Content Without a Director
~~~ sql
SELECT * 
FROM netflix
WHERE director IS NULL;
~~~
### 13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
~~~ sql
SELECT * 
FROM netflix
WHERE cast LIKE '%Salman Khan%'
  AND release_year >= YEAR(GETDATE()) - 10;
~~~
### 14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
~~~ sql
SELECT TOP 10
    value AS actor,
    COUNT(*) AS total_content
FROM netflix
CROSS APPLY STRING_SPLIT(cast, ',')
WHERE country LIKE '%India%'
GROUP BY value
ORDER BY total_content DESC;
~~~
### 15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
~~~ sql
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description LIKE '%kill%' OR description LIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
~~~

















  
  
  


