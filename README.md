# # Netflix Movies and TV Shows Data Analysis using SQL

![Netflix image](https://github.com/SharmaHimali/netflix_sql_project/blob/main/logo.png)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

## Business Problems and Solutions

### 1. Count the Number of Movies vs TV Shows

```sql
select type,
count(*) as Total_Count
from flix
group by type;
```

**Objective:** Determine the distribution of content types on Netflix.

### 2. Find the Most Common Rating for Movies and TV Shows

```sql
select type,
rating
from
(
select type,
rating,
count(*),
rank () over(partition by type order by count(*) desc) as ranking
from flix
group by type,rating) as t
where ranking = 1;
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```

**Objective:** Identify the most frequently occurring rating for each type of content.

### 3. List All Movies Released in a Specific Year (e.g., 2020)

```sql
select * 
from flix
where release_year = '2020';
```

**Objective:** Retrieve all movies released in a specific year.

### 4. Find the Top 5 Countries with the Most Content on Netflix

```sql
select country,
count(show_id) as Most_Content
from flix
group by country
order by Most_Content desc
limit 5;
```

**Objective:** Identify the top 5 countries with the highest number of content items.

### 5. Identify the Longest Movie

```sql
select *
 from flix
 where type = 'Movie'
 &&
 duration = (select max(duration) from flix);
```

**Objective:** Find the movie with the longest duration.

### 6. Find Content Added in the Last 5 Years

```sql
select * from flix 
where release_year = year(curdate()) - 5;
```

**Objective:** Retrieve content added to Netflix in the last 5 years.

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
select *
from flix
where director ='Rajiv Chilaka';
```

**Objective:** List all content directed by 'Rajiv Chilaka'.

### 8. List All TV Shows with More Than 5 Seasons

```sql
select * 
from flix
where type = 'Tv Show'
&&
cast(substring_index(duration,' ', 1) as unsigned) >=5;
```

**Objective:** Identify TV shows with more than 5 seasons.

### 9. Count the Number of Content Items in Each Genre

```sql
SELECT trim(genre) as genre,
count(*) as total_content	
FROM flix
join json_table(
concat ('["',replace(listed_in,',','","'), '"]'),
'$[*]' columns (genre varchar(255) path '$')
) as jt
GROUP BY genre
order by total_content desc;
```

**Objective:** Count the number of content items in each genre.

### 10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release!

```sql
select year(str_to_date(date_added,'%M %d, %Y')) as year,
count(*) as Total_Count,
round(count(*)/(select count(*) from flix where country = 'India') * 100,2) as avg_content
from flix
where country = 'India'
group by year
order by Total_Count desc
limit 5;
```

**Objective:** Calculate and rank years by the average number of content releases by India.

### 11. List All Movies that are Documentaries

```sql
select * 
from flix
where type = 'Movie'
&&
listed_in = 'documentaries';

```

**Objective:** Retrieve all movies classified as documentaries.

### 12. Find All Content Without a Director

```sql
select * 
from flix
where director is null;
```

**Objective:** List content that does not have a director.

### 13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

```sql
select type,
count(*) as Appeared_Movie
from flix
where `cast` like '%Salman Khan%'
&&
release_year >= year(curdate()) - 10
&&
type = 'Movie'
group by type;
```

**Objective:** Count the number of movies featuring 'Salman Khan' in the last 10 years.

### 14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

```sql
select trim(actor) as Actor,
count(*) as total_content
from flix
join json_table(
concat ('["', replace(cast,',','","'),'"]'),
'$[*]' columns (actor varchar(255) path '$')
) as at
where country ='India'
group by actor
order by total_content desc
limit 10;
```

**Objective:** Identify the top 10 actors with the most appearances in Indian-produced movies.

### 15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

```sql
select category,
type,
count(*) as content_count 
from
(
select *,
case 
when description like '%kill%' or '%violence%' then 'Bad'
else 'Good'
end as category
from flix 
) as categorized_content
group by category,type
order by type
```

**Objective:** Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.



