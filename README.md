# Spotify SQL Project 

![Image](https://github.com/user-attachments/assets/cc8d6fd1-f7fb-4f41-88d2-baac90f92ad0)

SQL Spotify Data Analysis Project

This project explores Spotify music data using PostgreSQL. It involves designing a relational schema, performing data cleaning, and running insightful SQL queries to analyze trends in music consumption, artist activity, and platform engagement.

---

##  Project Highlights

-  Cleaned data ( removed zero-duration tracks).
- Performed exploratory data analysis (EDA) to understand:
  - Distribution of artists, albums, and track types
  - Views, likes, streams, and platform breakdown
-  Answered key business questions using SQL queries.
-  Applied advanced SQL concepts like:
  - `GROUP BY`, `ORDER BY`, `DISTINCT`
  - Window Functions(`DENSE_RANK`)
  - CTEs (Common Table Expressions)
  - Conditional aggregation (`CASE WHEN`)

---

## Tools & Technologies

- **Language**: SQL  
- **Database**: PostgreSQL  




---

1) RETRIEVE THE NAMES OF ALL TRACKS THAT HAVE MORE THAN 1 BILLION STREAMS
   
``` sql
 SELECT COUNT(*) FROM SPOTIFY
WHERE STREAM > 1000000000; ```

2)LIST ALL ALBUMS ALONG WITH THEIR RESPECTIVE ARTISTS

``` sql
SELECT 
DISTINCT ALBUM, ARTIST
FROM SPOTIFY ORDER BY ARTIST DESC; ```


3) GET THE TOTAL NUMBER OF COMMENTS FOR TRACKS WHERE LICENSED = TRUE.

``` sql
SELECT 
SUM(COMMENTS) as TOTAL_COMMENTS
FROM SPOTIFY 
WHERE LICENSED = 'true'; ```

4) FIND ALL TRACKS THAT BELONG TO THE ALBUM TYPE SINGLE

``` sql
SELECT * FROM SPOTIFY 
WHERE ALBUM_TYPE LIKE 'single'; ```


5) COUNT THE TOTAL NUMBER OF TRACKS BY EACH ARTIST

``` sql
SELECT ARTIST,
COUNT(*) AS TOTAL_NO_SONGS
FROM SPOTIFY
GROUP BY ARTIST
ORDER BY 2; ```


6) Calculate the average danceability of tracks in each album.

``` sql
SELECT ALBUM, AVG(DANCEABILITY) AS AVG_DANCEABILITY
FROM SPOTIFY
GROUP BY ALBUM
ORDER BY AVG_DANCEABILITY DESC; ```


7) Find the top 5 tracks with the highest energy values.

``` sql
SELECT TRACK, MAX(ENERGY) AS MAX_ENERGY FROM SPOTIFY
GROUP BY TRACK
ORDER BY MAX_ENERGY  DESC
LIMIT 5
; ```

8) List all tracks along with their views and likes where official_video = TRUE.

``` sql
SELECT TRACK,
SUM(VIEWS) AS TOTAL_VIEWS,
SUM(LIKES) AS TOTAL_LIKES
FROM SPOTIFY
WHERE OFFICIAL_VIDEO = 'true'
GROUP BY TRACK
ORDER BY TOTAL_VIEWS DESC
LIMIT 5; ```


9) For each album, calculate the total views of all associated tracks.

``` sql SELECT DISTINCT ALBUM,TRACK,SUM(VIEWS) AS TOTAL_VIEWS
FROM SPOTIFY
GROUP BY ALBUM, TRACK
ORDER BY TOTAL_VIEWS DESC; ```


10) Retrieve the track names that have been streamed on Spotify more than YouTube.

``` sql SELECT * FROM
(SELECT TRACK,
-- MOST_PLAYED_TRACKS
COALESCE(SUM(CASE WHEN MOST_PLAYED_ON = 'Youtube' THEN STREAM END),0) AS STREAMED_ON_YOUTUBE,
COALESCE(SUM(CASE WHEN MOST_PLAYED_ON = 'Spotify' THEN STREAM END),0) AS STREAMED_ON_SPOTIFY
FROM SPOTIFY
GROUP BY TRACK) AS T1
WHERE STREAMED_ON_SPOTIFY > STREAMED_ON_YOUTUBE
AND
STREAMED_ON_SPOTIFY <> 0; ```

11) Find the top 3 most-viewed tracks for each artist using window functions.

``` sql
WITH RANKING_ARTIST
AS
(SELECT  ARTIST, TRACK, SUM(VIEWS) AS TOTAL_VIEW, 
DENSE_RANK() OVER(PARTITION BY ARTIST ORDER BY SUM(VIEWS) DESC) AS RANK
FROM SPOTIFY
GROUP BY ARTIST,TRACK
ORDER BY ARTIST, TOTAL_VIEW DESC)
SELECT * FROM RANKING_ARTIST
WHERE RANK <=3; ```


12) Write a query to find tracks where the liveness score is above the average.

``` sql
SELECT TRACK,
ARTIST,
LIVENESS
FROM SPOTIFY
WHERE LIVENESS > (SELECT AVG(LIVENESS) FROM SPOTIFY); ```

13) Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.

``` sql
WITH CTE AS
(SELECT ALBUM,
MAX(ENERGY) AS HIGHEST_ENERGY,
MIN(ENERGY) AS LOWEST_ENERGY
FROM SPOTIFY
GROUP BY 1)
SELECT ALBUM, HIGHEST_ENERGY - LOWEST_ENERGY AS ENERGY_DIFF
FROM CTE
ORDER BY ALBUM, ENERGY_DIFF DESC; ```
```
