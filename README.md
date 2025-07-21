# SQL-Project-Spotify-Data-Analysis

```sql

-- Personal Study - SQL Project Spotify Dataset Data Analysis

-- Create Table Query
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

-- Exploratory Data Analysis

SELECT COUNT(*) -- 20594 tracks
FROM spotify;

SELECT COUNT(DISTINCT artist) -- 2074 artists
FROM spotify;

SELECT COUNT(DISTINCT album) -- 11854 albums
FROM spotify;

SELECT COUNT(DISTINCT album_type) -- 3 album types
FROM spotify;

SELECT MAX(duration_min) -- 77 mins
FROM spotify;

SELECT MIN(duration_min) -- 0 mins (requires filtering)
FROM spotify;

SELECT * 
FROM spotify 
WHERE duration_min = 0; -- 2 Songs

DELETE FROM spotify 
WHERE duration_min = 0; -- dropping outliers

SELECT DISTINCT channel 
FROM spotify; -- 6673 channels (publishers)

SELECT DISTINCT most_played_on
FROM spotify; -- 2 results (Youtube & Spotify)

-- 1. Basic Questions 

-- 1.1 Retrieve the names of all tracks that have more than 1 billion streams.

SELECT * 
FROM spotify
WHERE stream > 1000000000; -- 385 Tracks with over a billion streams

-- 1.2 List all albums along with their respective artists.

SELECT DISTINCT album, artist
FROM spotify
ORDER BY artist;

-- 1.3 Get the total number of comments for tracks where licensed = TRUE.

SELECT SUM(comments) as total_comments
FROM spotify 
WHERE licensed = 'true'; -- 497015695 total comments.

-- 1.4 Find all tracks that belong to the album type single.

SELECT *
FROM spotify
WHERE album_type = 'single'; -- 4973 singles listed as an album.

-- 1.5 Count the total number of tracks by each artist.

SELECT artist, COUNT(*) as number_of_songs
FROM spotify
GROUP BY artist
ORDER BY 2 DESC;

-- 2. Medium Level Questions

-- 2.1 Calculate the average danceability of tracks in each album.

SELECT album, AVG(danceability) as avg_danceability
FROM spotify
GROUP BY album ORDER BY avg_danceability DESC;

-- 2.2 Find the top 5 tracks with the highest energy values.

SELECT track, MAX(energy) 
FROM spotify
GROUP BY track ORDER BY MAX(energy) DESC LIMIT 5;

-- 2.3 List all tracks along with their views and likes where official_video = TRUE.

SELECT 
     track, 
	 SUM(views) as total_views, 
	 SUM(likes) as total_likes
FROM spotify
WHERE official_video = 'true'
GROUP BY track ORDER BY total_views DESC; 

-- 2.4 For each album, calculate the total views of all associated tracks.

SELECT 
     album, 
	 track, 
	 SUM(views) as total_views
FROM spotify
GROUP BY album, track ORDER BY total_views DESC;

-- 2.5 Retrieve the track names that have been streamed on Spotify more than YouTube.

SELECT 
     track, 
	 stream, 
	 most_played_on 
FROM spotify
WHERE most_played_on = 'Spotify'
ORDER BY stream DESC;

-- 3. Advanced Level Questions

-- 3.1 Find the top 3 most-viewed tracks for each artist using window functions.

WITH track_rank
AS
(SELECT 
      artist, 
	  track, 
	  SUM(views) as total_views, 
	  DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) as rank 
FROM spotify
GROUP BY artist, track 
ORDER BY artist, total_views DESC)
SELECT * FROM track_rank
WHERE rank <= 3

-- 3.2 Write a query to find tracks where the liveness score is above the average.

SELECT track, artist, liveness 
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify)

-- 3.3 Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.

WITH cte -- Common Table Expression
AS
(SELECT
      album, 
	  MAX(energy) as max_energy,
	  MIN(energy) as min_energy
FROM spotify
GROUP BY album
)
SELECT 
     album,
	 max_energy - min_energy as energy_diff
FROM cte
ORDER BY energy_diff DESC;

```

-- Next Steps: Create a Dashboard - POWER BI/ Tableau
