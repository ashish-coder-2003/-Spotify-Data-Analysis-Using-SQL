# Spotify Advanced SQL Project and Query Optimization
Project Category: Advanced

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance.
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
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

#### Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
  
#### Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.

### 5. Query Optimization
In advanced stages, the focus shifts to improving query performance. Some optimization strategies include:
- **Indexing**: Adding indexes on frequently queried columns.
- **Query Execution Plan**: Using `EXPLAIN ANALYZE` to review and refine query performance.
  
---

## 15 Practice Questions
```sql
### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams.

select count(track) from spotify
where stream>1000000000;

2. List all albums along with their respective artists.

select distinct album, artist from spotify
order by 1;

select distinct album from spotify
order by 1;
   
3. Get the total number of comments for tracks where `licensed = TRUE`.

select sum(comments) as total_comments from spotify
where licensed = 'true';
   
4. Find all tracks that belong to the album type `single`.

select track, album_type from spotify
where album_type = 'single';

5. Count the total number of tracks by each artist.

select artist, count(*) as total_songs from spotify
group by artist
order by 2;

### Medium Level
1. Calculate the average danceability of tracks in each album.

SELECT album, avg(danceability) as avg_danceability from spotify
group by 1
order by 2 desc;

2. Find the top 5 tracks with the highest energy values.

select track, avg(energy) as max from spotify
group by 1
order by 2 desc
limit 5;

3. List all tracks along with their views and likes where `official_video = TRUE`.

select track, sum(views) as total_views, sum(likes) as total_likes from spotify
where official_video = 'true'
group by 1
order by 2 desc;

4. For each album, calculate the total views of all associated tracks.

select album, sum(views) as total_views, track from spotify
group by 1,3
order by 2 desc;

5. Retrieve the track names that have been streamed on Spotify more than YouTube.

SELECT * FROM
(select 
 track,
 --most_played_on,
 COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END),0) as str_youtube,
 COALESCE(SUM(CASE WHEN most_played_on = 'spotify' THEN stream END),0) as str_spotify
 from spotify
 GROUP BY 1
 ) as t1
 WHERE str_spotify > str_youtube
 AND
 str_youtube <> '0';

### Advanced Level
1. Find the top 3 most-viewed tracks for each artist using window functions.

with ranking_artist
as
(SELECT 
 artist,
 track,
 SUM(views) as total_views,
 dense_rank() over(partition by artist order by sum(views) desc) as rank
from spotify
group by 1,2
order by 1,3 desc
)
select * from ranking_artist
where rank <= 3;

2. Write a query to find tracks where the liveness score is above the average.

select track, liveness as above_avg_liveness from spotify
where liveness > (select avg(liveness) from spotify);

3. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**

with cte
as
(select 
 album,
 max(energy) as highest_energy,
 min(energy) as lowest_energy
from spotify
group by 1)

select 
 album,
 highest_energy - lowest_energy as energy_diff
from cte
order by 2 desc;
```
