# 🎵 Spotify SQL Project

## 📑 Table of Contents  
- [📌 Project Overview](#-project-overview)  
- [🛠️ Tools](#️-tools)  
- [🧹 Data Cleaning / Preparation](#-data-cleaning--preparation)  
- [📊 Exploratory Data Analysis (EDA)](#-exploratory-data-analysis-eda)  
- [🔎 Data Analysis](#-data-analysis)  
- [📈 Results](#-results)  
- [💡 Business Recommendations / Next Steps](#-business-recommendations--next-steps)  
- [⚠️ Limitations](#-limitations)  

---

## 📌 Project Overview  
This project focused on analyzing a **Spotify dataset** containing track, album, and artist-level attributes such as streams, views, likes, comments, and audio features (energy, danceability, loudness, etc.).  

We were asked to:  
- **Structure and clean the raw dataset** into a usable SQL table.  
- **Remove inaccurate and missing data**, such as tracks with 0 duration.  
- **Explore the dataset** to understand artist, album, and streaming platform distributions.  
- **Perform advanced SQL analysis** to answer business-driven questions.  
- **Optimize queries** to improve performance using indexing and execution plans.  

The solution included:  
- Designing a SQL schema for the dataset.  
- Running exploratory queries to validate and clean data.  
- Solving key business problems through SQL queries, including:  
  - Identifying the **top 3 most-viewed tracks per artist**.  
  - Measuring **energy diversity across albums**.  
- Using **window functions and CTEs** to simplify advanced analysis.  
- Applying **query optimization** to reduce execution times.  

The impact of this project showed how structured SQL analysis can uncover **artist-defining tracks, album diversity, and streaming trends**, while also improving database performance with indexing.  

---

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```

## 🛠️ Tools  
- **PostgreSQL** – Database engine  
- **pgAdmin 4** – SQL environment for queries  
- **Excel/CSV** – Source data format  
- **Power BI / Tableau (future use)** – For visualization of query results


---

## 🧹 Data Cleaning / Preparation  
- Created a `Spotify` table with structured columns for **artist, track, album, streams, likes, comments, and audio features**.  
- Removed invalid records (e.g., `duration_min = 0`).  
- Standardized `album_type` values (`album` vs `single`).  
- Ensured boolean fields (`licensed`, `official_video`) contained valid values.  
- Validated streaming source categories (`most_played_on`).  

---

## 📊 Exploratory Data Analysis (EDA)  
- Counted **total tracks, albums, and artists** in the dataset.  
- Checked **distribution of album types** (albums vs singles).  
- Analyzed **track duration** (minimum and maximum length).  
- Reviewed **distinct channels** and platforms (`Spotify`, `YouTube`).  
- Verified overall data consistency before deep analysis.  

---

## 🔎 Data Analysis  

### 3. Top 3 Most-Viewed Tracks for Each Artist (Window Functions)  
We identified the most popular tracks for each artist by ranking songs based on views.  

```sql
WITH ranking_artists AS (
    SELECT 
        artist,
        track,
        SUM(views) AS total_views,
        DENSE_RANK() OVER(
            PARTITION BY artist 
            ORDER BY SUM(views) DESC
        ) AS rank
    FROM spotify
    GROUP BY artist, track
)
SELECT *
FROM ranking_artists
WHERE rank <= 3;
```
### What this solved:  

- Found the most impactful tracks for every artist.  
- Helped identify which songs drive engagement on streaming platforms.  
- Useful for concert planning, playlist building, and promotional focus.  

---

### 4. Energy Difference per Album (CTE)  

We measured musical diversity within albums by calculating the energy range.  
```sql
WITH cte AS (
    SELECT 
        album,
        MAX(energy) AS highest_energy,
        MIN(energy) AS lowest_energy
    FROM spotify
    GROUP BY album
)
SELECT 
    album,
    highest_energy - lowest_energy AS energy_diff
FROM cte
ORDER BY energy_diff DESC;
```
### What this solved:  

- Identified albums with wide musical variety (calm and energetic songs).  
- Showed albums with consistent energy, useful for branding and mood playlists.  
- Provided insights into album style, helping platforms recommend music more accurately.  

---

## 📈 Results  

- Dataset cleaned and structured for SQL analysis.  
- Top 3 tracks per artist extracted successfully, showing their biggest songs.  
- Energy diversity per album calculated, showing which albums are versatile vs consistent.  
- Demonstrated the use of window functions and CTEs in real-world SQL cases.  
- Optimized queries to reduce execution time, making large-scale queries faster.  

---

## 💡 Business Recommendations / Next Steps  

- Use top artist tracks for targeted marketing campaigns.  
- Build mood-based playlists by classifying albums with high or low energy diversity.  
- Add release year and genre to expand time-based and category insights.  
- Integrate data into Power BI/Tableau dashboards for non-technical stakeholders.  
- Continue experimenting with query optimization on larger datasets.  

---

## ⚠️ Limitations  

- Dataset lacked release years and genres, limiting deeper insights.  
- Only included Spotify and YouTube streams, not Apple Music, SoundCloud, etc.  
- Some engagement metrics (e.g., likes, comments) don’t measure listener retention or skip rates.  
- Cleaning assumptions (e.g., removing zero-duration tracks) may slightly affect results.  
