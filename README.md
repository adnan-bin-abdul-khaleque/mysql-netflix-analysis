# 🎬 Netflix Content Analysis Using MySQL

## 📌 Project Overview

This project demonstrates SQL-based data analysis on a Netflix dataset using **MySQL**. It includes database creation, data importing, view creation, exploratory data analysis, business insights, and advanced SQL techniques such as **Common Table Expressions (CTEs)** and **Window Functions**.

The objective is to analyze Netflix movies and TV shows to uncover insights about ratings, popularity, countries, directors, genres, release trends, and audience preferences.

---

# 🗂 Database

**Database Name:** `netflix_db`

---

# 🛠 Technologies Used

* MySQL
* SQL
* MySQL Workbench

---

# 📂 Dataset

The project uses two datasets:

* **netflix_titles** (Movies)
* **tv_shows** (TV Shows)

The datasets include information such as:

* Show ID
* Title
* Type
* Director
* Cast
* Country
* Date Added
* Release Year
* Rating
* Duration
* Genres
* Language
* Description
* Popularity
* Vote Count
* Vote Average
* Budget *(Movies only)*
* Revenue *(Movies only)*

---

# Database Setup

## 1. Create Database

```sql
CREATE DATABASE netflix_db;
```

---

## 2. Create Tables

The project creates two tables:

* `netflix_titles`
* `tv_shows`

Each table stores Netflix content information using appropriate SQL data types.

---

## 3. Import Dataset

The datasets are imported using **LOAD DATA INFILE**.

```sql
LOAD DATA INFILE 'C:/csv_file_path'
INTO TABLE netflix_titles
...
```

The same method is used for the **tv_shows** table.

---

## 4. Create View

A SQL View named **netflix_content** combines Movies and TV Shows into a single virtual table.

```sql
CREATE VIEW netflix_content AS
SELECT ...
FROM netflix_titles

UNION ALL

SELECT ...
FROM tv_shows;
```

This view simplifies analysis by allowing queries on both datasets simultaneously.

---

# General Analysis Queries

## 1. Top 10 Highest-Rated Titles

Find the highest-rated titles based on rating and popularity.

```sql
SELECT
    title,
    type,
    country,
    language,
    release_year,
    rating,
    popularity
FROM netflix_content
ORDER BY rating DESC, popularity DESC
LIMIT 10;
```

---

## 2. Count Movies and TV Shows

Determine the number of Movies and TV Shows.

```sql
SELECT
    type,
    COUNT(*) AS total_titles
FROM netflix_content
GROUP BY type;
```

---

## 3. Top 10 Countries with the Most Titles

```sql
SELECT
    country,
    COUNT(*) AS total_titles
FROM netflix_content
GROUP BY country
ORDER BY total_titles DESC
LIMIT 10;
```

---

## 4. Average Rating by Language

```sql
SELECT
    language,
    AVG(rating) AS avg_rating
FROM netflix_content
GROUP BY language
ORDER BY avg_rating DESC;
```

---

## 5. Directors with the Most Titles

Find directors who have directed at least **10 titles**.

```sql
SELECT
    director,
    COUNT(title) AS total_titles
FROM netflix_titles
GROUP BY director
HAVING total_titles >= 10
ORDER BY total_titles DESC;
```

---

## 6. Genres with the Highest Average Rating

```sql
SELECT
    genres,
    AVG(rating) AS avg_rating
FROM netflix_content
GROUP BY genres
ORDER BY avg_rating DESC;
```

---

## 7. Top 15 Most Popular Movies

```sql
SELECT
    title,
    director,
    release_year,
    popularity,
    country
FROM netflix_titles
ORDER BY popularity DESC
LIMIT 15;
```

---

## 8. Top 15 Most Popular TV Shows

```sql
SELECT
    title,
    release_year,
    country,
    popularity
FROM tv_shows
ORDER BY popularity DESC
LIMIT 15;
```

---

## 9. Countries with Above-Average Popularity

```sql
SELECT
    country,
    AVG(popularity) AS avg_popularity
FROM netflix_titles
GROUP BY country
HAVING avg_popularity >
(
    SELECT AVG(popularity)
    FROM netflix_titles
)
ORDER BY avg_popularity DESC;
```

---

## 10. Release Trend by Year

Display the number of titles released each year.

```sql
SELECT
    release_year,
    COUNT(title) AS total_titles
FROM netflix_titles
GROUP BY release_year
ORDER BY release_year DESC;
```

---

# Advanced SQL Queries

## 11. Rank Movies by Rating Within Each Release Year

Uses the **RANK() Window Function**.

```sql
SELECT
    release_year,
    title,
    rating,
    RANK() OVER(
        PARTITION BY release_year
        ORDER BY rating DESC
    ) AS movie_rank
FROM netflix_titles
ORDER BY release_year, movie_rank;
```

---

## 12. Top-Rated Movie from Each Country

Uses a **Common Table Expression (CTE)** and **Window Function**.

```sql
WITH ranked_movies AS (

SELECT
    release_year,
    country,
    title,
    rating,

    RANK() OVER(
        PARTITION BY country
        ORDER BY rating DESC
    ) AS rn

FROM netflix_titles

)

SELECT
    title,
    country,
    rating
FROM ranked_movies
WHERE rn = 1
ORDER BY country;
```

---

## 13. Running Total of Vote Counts by Year

```sql
WITH yearly_votes AS (

SELECT
    release_year,
    SUM(vote_count) AS yearly_votes

FROM netflix_titles

GROUP BY release_year

)

SELECT
    release_year,
    yearly_votes,

    SUM(yearly_votes) OVER(
        ORDER BY release_year
    ) AS running_total

FROM yearly_votes
ORDER BY release_year;
```

---

## 14. Compare Movie Rating with Yearly Average

```sql
SELECT

title,

release_year,

rating,

AVG(rating) OVER(
PARTITION BY release_year
) AS avg_rating,

rating -

AVG(rating) OVER(
PARTITION BY release_year
) AS diff_rating

FROM netflix_titles

ORDER BY release_year, rating DESC;
```

---

## 15. Find Titles Appearing in Both Datasets

```sql
SELECT
    n.title
FROM netflix_titles n
INNER JOIN tv_shows t
ON n.title = t.title;
```

---

## 16. Compare Average Ratings of Movies and TV Shows

```sql
SELECT
    type,
    AVG(rating) AS avg_rating
FROM netflix_content
GROUP BY type;
```

---

## 17. Directors Who Worked on Both Movies and TV Shows

```sql
SELECT
    n.director,
    n.title AS movie,
    t.title AS tv_show
FROM netflix_titles n
INNER JOIN tv_shows t
ON n.director = t.director;
```

---

## 18. Top Directors by Weighted Rating Score

Weighted score is calculated using:

**Rating × Vote Count**

```sql
SELECT

director,

type,

SUM(rating * vote_count) AS weighted_score

FROM netflix_content

WHERE director IS NOT NULL
AND director <> ''

GROUP BY director, type

ORDER BY weighted_score DESC

LIMIT 5;
```

---

## 19. Genre Popularity Before and After 2017

Compare how genre popularity has changed over time.

```sql
SELECT

genres,

CASE
WHEN release_year < 2017 THEN 'Before 2017'
ELSE '2017 and After'
END AS period,

AVG(popularity) AS avg_popularity

FROM netflix_titles

GROUP BY

genres,

CASE
WHEN release_year < 2017 THEN 'Before 2017'
ELSE '2017 and After'
END

ORDER BY avg_popularity DESC;
```

---

# SQL Concepts Covered

* Database Creation
* Table Creation
* Data Import (`LOAD DATA INFILE`)
* SQL Views
* SELECT
* WHERE
* GROUP BY
* HAVING
* ORDER BY
* Aggregate Functions (`COUNT`, `AVG`, `SUM`)
* INNER JOIN
* UNION ALL
* Subqueries
* Common Table Expressions (CTEs)
* Window Functions (`RANK()`, `AVG() OVER`, `SUM() OVER`)
* CASE Statements
* Data Aggregation
* Ranking Analysis

---

# Key Business Insights

This project answers several real-world business questions, including:

* Which titles have the highest ratings?
* Which countries produce the most Netflix content?
* Which languages receive the highest average ratings?
* Which directors have created the most content?
* Which genres are the most highly rated?
* Which movies and TV shows are the most popular?
* Which countries consistently produce popular content?
* How has Netflix content changed over time?
* Which directors have contributed to both Movies and TV Shows?
* How has genre popularity evolved before and after 2017?

---

# Project Structure

```text
Netflix-SQL-Analysis/
│
├── netflix_analysis.sql
├── README.md
└── dataset/
    ├── netflix_titles.csv
    └── tv_shows.csv
```

---

# Learning Outcomes

Through this project, I practiced:

* Relational Database Design
* Data Import in MySQL
* SQL Views
* Data Exploration
* Business Intelligence Queries
* Window Functions
* Common Table Expressions (CTEs)
* SQL Joins
* Ranking Analysis
* Trend Analysis
* Popularity and Rating Analytics

---

## Author

**Adnan Bin Abdul Khaleque**

Aspiring Data Analyst passionate about transforming raw data into meaningful insights using SQL, Excel, Power BI, Tableau, and Python.


