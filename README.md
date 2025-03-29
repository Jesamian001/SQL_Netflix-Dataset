# SQL PROJECT: INSIGHTS INTO NETFLIX, UNDERSTANDING VIEWING PATTERNS AND CONTENT POPULARITY
![title](https://github.com/user-attachments/assets/96e553d4-c239-4569-bfc8-84d9701b3464)

## PROJECT DESCRIPTION
This project involves analyzing the Netflix Titles dataset, which includes detailed information about movies and TV shows available on the platform. The dataset contains 11 key columns, such as show ID, title, type (movie or TV show), director, actors, country, release year, rating, duration, genre, and description. By leveraging SQL queries, we aim to uncover trends related to content types, genres, release years, ratings, and other key aspects.

## DATABASE SCHEMA IMAGE
![database schema](https://github.com/user-attachments/assets/d476aafc-5309-4b87-9ec5-4d0115f3f517)

## DATABASE SCHEMA SQL
```sql
DROP TABLE IF EXISTS netflix_titles;
CREATE TABLE netflix_titles 
    (
        show_id VARCHAR(15) PRIMARY KEY,
        type VARCHAR(15),
        title VARCHAR(200),
        director VARCHAR(250),
        actors VARCHAR(1000), 
        country VARCHAR(150),
        date_added DATE,
        release_year YEAR,
        rating VARCHAR(100),
        duration VARCHAR(50),
        listed_in VARCHAR(150),
        description VARCHAR(500)
    );
```

## DATA CLEANING
### 1). FIND THE RECORDS(ROWS) FOR EACH ATTRIBUTE(COLUMNS) THAT IS NULL
```sql
SELECT * FROM netflix_titles
WHERE 
    type IS NULL
    OR
    title IS NULL
    OR
    director IS NULL
    OR
    actors IS NULL
    OR
    country IS NULL
    OR
    date_added IS NULL
    OR
    release_year IS NULL
    OR
    rating IS NULL
    OR
    duration IS NULL
    OR
    listed_in IS NULL
    OR
    description IS NULL
;
```
### 2). FIND THE RECORDS(ROWS) FOR EACH ATTRIBUTE(COLUMNS) THAT BLANK
```sql
SELECT * FROM netflix_titles
WHERE 
    type = ''
    OR
    title  = ''
    OR
    director  = ''
    OR
    actors  = ''
    OR
    country  = ''
    OR
    rating  = ''
    OR
    duration  = ''
    OR
    listed_in  = ''
    OR
    description  = ''
;
```
### 3). UPDATE THE DATE_ADDED COLUMN WITH THE MOST RECENT DATE WHERE DATE_ADDED IS NULL
```sql
--Check the most recent date
SELECT date_added
FROM netflix_titles
WHERE date_added IS NOT NULL
ORDER BY date_added DESC
LIMIT 1;

--Updates the date_added column where date_added is null
UPDATE netflix_titles
SET date_added = '2020-01-01'
WHERE date_added IS NULL;

--Check if it is updated
SELECT date_added FROM netflix_titles
WHERE date_added IS NULL;
```

### 4). UPDATE THE COLUMNS WHERE THE VALUE IS NULL WITH A PLACEHOLDER.
Replace the values null to 'Unknown'
```sql
--To update columns individually in a single query, use CASE.
UPDATE netflix_titles
SET 
    director = CASE 
                  WHEN director = '' THEN 'Unknown' 
                  ELSE director 
               END,
    actors = CASE 
                WHEN actors = '' THEN 'Unknown' 
                ELSE actors 
              END,
    country = CASE 
                WHEN country = '' THEN 'Unknown' 
                ELSE country 
              END,
    rating = CASE 
                WHEN rating = '' THEN 'Unknown' 
                ELSE rating 
              END,
    duration = CASE 
                 WHEN duration = '' THEN 'Unknown' 
                 ELSE duration 
               END
WHERE
    director = '' OR
    actors = '' OR
    country = '' OR
    rating = '' OR
    duration = '';
```

## BUSINESS QUESTIONS:
![business questions](https://github.com/user-attachments/assets/c21d02c2-9589-47c5-a215-281830def7a9)

## BUSINESS PROBLEMS AND SOLUTIONS

### 1). How Many Titles Are Released Per Year, and Is There a Trend in Content Production Over Time (release_year)?
```sql
SELECT release_year, COUNT(*) AS no_of_movies
FROM netflix_titles
WHERE title IS NOT NULL
GROUP BY release_year
ORDER BY release_year DESC;
```
### 2). What is the Trend in the Number of Titles Added Per Month (date_added)?
```sql
SELECT
    DATE_FORMAT(date_added, '%Y-%m') AS month, -- Extract year and month
    COUNT(*) AS title_count
FROM netflix_titles
WHERE date_added IS NOT NULL
GROUP BY month
ORDER BY month DESC;
```
### 3). Which Years Have the Least Content Released?
```sql
SELECT
    release_year, -- Extract year and month
    COUNT(*) AS title_count
FROM netflix_titles
WHERE date_added IS NOT NULL
GROUP BY release_year
ORDER BY title_count ASC;
```
### 4). What specific genres that dominate Netflix's content library (e.g., Comedy, Drama)?
```sql
SELECT
        TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(listed_in, ',', numbers.n), ',', -1)) AS genre,
        COUNT(*) AS genre_count
    FROM (          
    SELECT 1 AS n      
    UNION ALL SELECT 2  
    UNION ALL SELECT 3
    -- Add more unions if needed
) AS numbers
JOIN netflix_titles
ON  
numbers.n <= CHAR_LENGTH(listed_in) - CHAR_LENGTH(REPLACE(listed_in, ',', '')) + 1
GROUP BY genre
ORDER BY genre_count DESC;
```
### 5). Is there a seasonal pattern (Winter, Fall, Summer, Spring) to when content is added to Netflix?
```sql
SELECT
    CASE
        WHEN MONTH(date_added) IN (12, 1, 2) THEN 'Winter'
        WHEN MONTH(date_added) IN (3, 4, 5) THEN 'Spring'
        WHEN MONTH(date_added) IN (6, 7, 8) THEN 'Summer'
        ELSE 'Fall'
    END AS season,
    COUNT(*) AS title_count
FROM netflix_titles
WHERE date_added IS NOT NULL
GROUP BY season
ORDER BY title_count DESC;
```

## OVERALL INSIGHTS:

1). *Content Strategy:* Over time, Netflix’s content strategy seems to have shifted from simply licensing content to focusing more on creating original titles (Originals) as the platform matures. This could be reflected in the increase of Netflix Originals in recent years.

2). *Global Expansion:* The dominance of genres like Drama, Documentary, and Comedy could reflect Netflix’s global approach to appeal to various demographics and regions.

3). *Strategic Timing:* The trend of releasing content during specific months (e.g., holidays, summer) could show that Netflix has a well-planned content release calendar to maximize viewer engagement.

4). *Genre Diversification:* If Netflix has a broad range of genres, it may indicate that Netflix is attempting to cater to as many niche audiences as possible, whereas a dominant focus on a few genres could suggest a more targeted approach in order to build a loyal subscriber base.



