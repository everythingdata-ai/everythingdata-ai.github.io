---
layout: post
title: Dimensional Data Modeling - Data Expert Bootcamp
categories: [Data Engineering, Data Modeling]
---

After following [Zach Wilson](https://www.linkedin.com/in/eczachly/) for a while on LinkedIn, I decided to jump-in his free Data Engineering bootcamp.
The first week is about Dimensional Data Modeling, and I have been able to follow everything fairly easily, except a few new concepts like Slowly Changing Dimensions.

The bootcamp is available for free on Youtube [here](https://www.youtube.com/watch?v=myhe0LXpCeo&list=PLwUdL9DpGWU0lhwp3WCxRsb1385KFTLYE).

Here are my notes from the first week of the bootcamp.

## 1 - Dimensional Data Modeling

### What is a dimension ?
Attributes of an entity (user's birthday, favorite food...)
Some of the dimensions may IDENTIFY an entity (user's ID, social security number, device id...) while others are just attributes

Flavours of dimensions : 
Slowly-changing that are time dependant (favorite food...) or fixed (birthday...)

### Knowing your consumer : who's going to be using the data ?
Data analysts / scientists : should be very easy to query, not many complex data types
Other data engineers : should be compact and harder to query, nested types are okay (master data)
ML models : depends on the model and how its trained 
Customers : should be a very easy to interpret chart

### OLTP vs OLAP vs master data
Online transaction processing : optimized for low-latency/low-volume queries, mainly used for software engineering
Online analytical processing : optimized for large volume, GROUP BY queries, minimizes JOINs mainly used for data engineering
Master Data : the middle ground, optimized for completeness of entity definitions, deduped

OLTP and OLAP is a continuum : 
Production database snapshots (app data) -> Master Data (taking all the production data sets, still normalized) -> OLAP Cubes (flatten the data) -> Metrics

### Cumulative table design : 
Components : 2 dataframes (yesterday and today) -> FULL OUTER JOIN the two data frames together -> COALESCE ids and unchanging dimensions to keep everything around, Compute comulation metrics (e.g. days since) and combine arrayd and changing values -> hang onto all of history
Usages : growth analytics, state transition tracking

### The compactness vs usability tradeoff
- The most usable tables have no complex data type and can easily be manipulated with WHERE and GROUP BY

- The most compact tables (not human readable) are compressed as small as possible and can't be queried directly

- The middle-ground tables use complex data types (e.g. ARRAY, MAP and STRUCT), making querying trickier but also compacting more


When would you use each type : 
- Most compact : online systems where latency and data volumes matter a lot where consumers are usually highly technical
- Middle-ground : upstream staging / master data where the majority of users are other data engineers
- Most usable : when analytics is the main consumer and the majority of consumers are less technical

### Struct vs Array vs Map
- Struct : table inside a table 
Keys are rigidly defined, compression is good
Values can be any type

- Map : 
Keys are loosely defined, compression is okay
Values all have to be the same type

- Array : 
Ordinal (list) datasets
List of values have to be all of the same type


### Temporal Cardinality Explosions of Dimensions : 
When you add a temporal aspect to your dimensions and the cardinality increases by at least 1 order of magnitude

Example : Airbnb has over 6 million listings
If we want to know the nightly pricing and availability of each night for the next year that 365  x 6M or about 2 billion nights
Should this dataset be : 
- Listing-level with an array of nights ?

- Night-level with 2 billion rows ?
If you explode it out and need to join other dimensions, Spark shuffle will ruin your compression

If you do the sorting right, Parquet will keep these two about the same size

Run-length encoding compression : probably the most important compression technique in big data right now, it's why Parquet file format has become so successful

### What are Common Table Expressions (CTEs)?
A Common Table Expression (CTE) is the result set of a query which exists temporarily and for use only within the context of a larger query. Much like a derived table, the result of a CTE is not stored and exists only for the duration of the query.

CTEs, like database views and derived tables, enable users to more easily write and maintain complex queries via increased readability and simplification. This reduction in complexity is achieved by deconstructing ordinarily complex queries into simple blocks to be used, and reused if necessary, in rewriting the query.

```sql
-- define CTE: 
WITH Cost_by_Month AS (SELECT campaign_id AS campaign,        TO_CHAR(created_date, 'YYYY-MM') AS month,        SUM(cost) AS monthly_cost FROM marketing WHERE created_date BETWEEN NOW() - INTERVAL '3 MONTH' AND NOW() GROUP BY 1, 2 ORDER BY 1, 2) 
-- use CTE in subsequent query:
SELECT campaign, avg(monthly_cost) as "Avg Monthly Cost" FROM Cost_by_Month GROUP BY campaign ORDER BY campaign
```

## 2 - Slowly changing dimensions

#### What is a SDC ?
A SDC is an attribute that drifts over time, like your favorite food as a kid is usually not the same as a adult, unlike your birthday that never changes
SDC helps track values, the dimensions have a time frame

#### What is idempotency ?
Tracking SDC is important for Idempotency : the ability for your data pipeline to produce the same results in all environments regardless of when its ran, which is critical
Pipelines should produce the same results, regardless of :
- the day/hour you run it
- how many times you run it

#### What can make a pipeline non-idempotent ?
- INSERT INTO without TRUNCATE : always use MERGE or INSERT OVERWRITE instead, otherwise you'll keep duplicating the data (INSERT INTO should be voided even with TRUNCATE)
- Using 'start_date >' without a corresponding 'end_date <'
- Not using a full set of partition sensors 
- Not using depends_on_past for cumulative pipelines
- Relying on the "latest" partition of a not properly modeled SCD table
- Relying on the "latest" partition of anything else

#### What are the other options to model change ?
- Singular snapshot : latest snapshot or Daily/Monthly/Yearly snapshot
- Daily partitioned snapshots
- SCD types 1, 2 ,3

#### Is SCD a good way to model your data ?
It depends how slowly changing are the dimensions
Basically SCD is a way of collapsing daily snapshots based on whether the data changed from day to day, instead of having 365 rows that say I'm 30, you have 1 row that says you're 30 from January 1st to December 13st

#### Types of SCD : 
- Type 0 : the value never changes
- Type 1 : You only care about the latest value, never should be used because it makes the pipeline not idempotent
- Type 2 : You care about what the value was from "start_date" to "end_date"
Current values usually have an end date that either NULL or very far into the future (9999-12-31). There's also usually a boolean "is_current" column. 
It's hard to use since there's more than 1 row per dimension. It's the only type of SCD that is purely idempotent 
- Type 3 : You only care about the "original" and "current" value
It's a middle ground that only has 1 row per dimension, but at the same time you lose the history. It's only partially idempotent

#### Ways to load SCD2 data
- Load the entire history in one query : inefficient but simble
- Incrementally load the data after the previous SCD is generated : efficient but cumbersome
### What are Windows functions in SQL ?
https://www.youtube.com/watch?v=y1KCM8vbYe4

Window functions perform  aggregate operations on groups of rows, but they produce a result for each row 

Example : this query allows to compare a player's number of points during a season to the average of all players, as well as his previous year's and next year's number of points

```sql
SELECT player_name,  
       pts,  
       AVG(pts) OVER (PARTITION BY season),  
       LAG(pts) OVER (PARTITION BY player_name ORDER BY player_name, season ASC) as pts_previous_season,  
       LEAD(pts) OVER (PARTITION BY player_name ORDER BY player_name, season ASC) as pts_next_season,  
       season  
FROM player_seasons;
```

## 3 - Graph Data Modeling

### Difference with relational DM
It's RELATIONSHIP focused, not ENTITY focused
Shines to show how things are connected
Trade-off : you don't have a schema around the property, the schemas are very flexible in graphs

Usual graph database model :
- Identifier : STRING
- Type : STRING
- Properties : MAP<STRING, STRING>

### Additive dimensions 
A dimension is additive over a specific window of time, if and only if, the grain of data over that window can only ever be one value at a time!

Additive dimensions mean that you don't "double count"

Example of additive dimension : Age, the population is equal to 20 year olds + 30 year olds + ...
Example of non-additive dimension : Number of active users != # web users + # android users + # iphone users

### When should you use enums ?
Enums get you :
- built in data quality, if you get a value that is not in the enums, the pipeline fails
- built in static fields
- built in documentation

Enumerations make amazing sub partitions because : 
- you have an exhaustive list
- they chunk up the big data problem into manageable pieces
Enums are great for low-to-medium cardinality
Rule of thumb : less than 50

### What type of use cases is this enum pattern useful?
Whenever you have tons of sources mapping to a shared schema 
- Airbnb: - Unit Economics (fees, coupons, credits, insurance, infrastructure cost, taxes, etc) - 
- Netflix: - Infrastructure Graph (applications, databases, servers, code bases, CI/CD jobs, etc) 
- Facebook - Family of Apps (oculus, instagram, facebook, messenger, whatsapp, threads, etc)
### How to model data from disparate sources into a shared schema ?
Flexible schema : leverage multiple map data types

Benefits :
- You don’t have to run ALTER TABLE commands 
- You can manage a lot more columns 
- Your schemas don’t have a ton of “NULL” columns 
- “Other_properties” column is pretty awesome for rarely-used-but-needed columns  

Drawbacks :
- Compression is usually worse (especially if you use JSON) 
- Readability, queryability

### Use case : NBA 
```sql
CREATE TYPE vertex_type  
    AS ENUM ('player', 'team', 'game');  
  
CREATE TYPE edge_type  
    AS ENUM ('plays_against',  
        'shares_team',  
        'plays_in', --plays in a game  
        'plays_on'); --plays on a team  
  
CREATE TABLE vertices (  
    identifier TEXT,  
    type vertex_type,  
    properties JSON,  
    PRIMARY KEY (identifier, type)  
);  
  
CREATE TABLE edges (  
  subject_identifier TEXT,  
  subject_type vertex_type,  
  object_identifier TEXT,  
  object_type vertex_type,  
  edge_type edge_type,  
  properties JSON,  
  PRIMARY KEY (subject_identifier,  
              subject_type,  
              object_identifier,  
              object_type,  
              edge_type  
              )  
);  
  
-- Games  
INSERT INTO vertices  
SELECT game_id AS identifier,  
       'game'::vertex_type AS type,  
       json_build_object(  
    'pts_home', pts_home,  
       'pts_away', pts_away,  
       'winning_team', CASE WHEN home_team_wins = 1 THEN home_team_id ELSE visitor_team_id END  
       ) as properties  
FROM games;  
  
-- Players  
INSERT INTO vertices  
WITH players_agg AS (  
SELECT  
    player_id AS identifier,  
    MAX(player_name) AS player_name,  
    COUNT(1) as number_of_games,  
    SUM(pts) as total_points,  
    ARRAY_AGG(DISTINCT team_id) AS teams  
FROM game_details  
GROUP BY player_id  
)  
SELECT identifier,  
       'player'::vertex_type,  
       json_build_object('player_name', player_name,  
       'number_of_game', number_of_games,  
       'total_points', total_points,  
       'teams', teams  
        )  
FROM players_agg;  
  
-- Teams  
INSERT INTO vertices  
WITH teams_deduped AS (  
    SELECT *, ROW_NUMBER() OVER(PARTITION BY team_id) as row_num  
    FROM teams  
)  
SELECT  
    team_id AS identifier,  
    'team'::vertex_type AS type,  
    json_build_object(  
    'abbreviation', abbreviation,  
    'nickname', nickname,  
    'city', city,  
    'arena', arena,  
    'year_founded', yearfounded  
    )  
FROM teams_deduped  
WHERE row_num = 1;  
  
INSERT INTO edges  
WITH games_deduped AS (  
    SELECT *, row_number() over (PARTITION BY game_id, player_id) AS row_num  
    FROM game_details  
)  
SELECT  
    player_id AS subject_identifier,  
    'player'::vertex_type as subject_type,  
    game_id AS object_identifier,  
    'game'::vertex_type AS obkect_type,  
    'plays_in'::edge_type AS edge_type,  
    json_build_object(  
    'start_position', start_position,  
    'pts', pts,  
    'team_id', team_id,  
    'team_abbreviation', team_abbreviation  
    ) as properties  
FROM games_deduped  
WHERE row_num = 1;  
  
  
INSERT INTO edges  
WITH games_deduped AS (  
    SELECT *, row_number() over (PARTITION BY game_id, player_id) AS row_num  
    FROM game_details  
),  
    filtered AS (  
        SELECT * FROM games_deduped  
                 WHERE row_num = 1  
    ),  
    aggregated AS (  
       SELECT  
       f1.player_id AS subject_player_id,  
       MAX(f1.player_name) AS subject_player_name,  
       f2.player_id AS object_player_id,  
       MAX(f2.player_name) AS object_player_name,  
       CASE WHEN f1.team_abbreviation = f2.team_abbreviation THEN 'shares_team'::edge_type  
        ELSE 'plays_against'::edge_type END AS edge_type,  
    COUNT(1) AS num_games,  
    SUM(f1.pts) AS subject_points,  
    SUM(f2.pts) AS object_points  
    FROM filtered f1 JOIN filtered f2  
    ON f1.game_id = f2.game_id  
    AND f1.player_name <> f2.player_name  
    WHERE f1.player_name > f2.player_name  
    GROUP BY f1.player_id,  
       f2.player_id,  
       CASE WHEN f1.team_abbreviation = f2.team_abbreviation THEN 'shares_team'::edge_type  
        ELSE 'plays_against'::edge_type END  
    )  
SELECT  
    subject_player_id AS subject_identifier,  
    'player'::vertex_type AS subject_type,  
     object_player_id AS object_identifier,  
    'player'::vertex_type AS object_type,  
    edge_type AS edge_type,  
    json_build_object(  
    'num_games', num_games,  
    'subject_points', subject_points,  
    'object_points', object_points  
    ) AS properties  
  
FROM aggregated;  
  
SELECT  
    v.properties->>'player_name',  
    e.object_identifier,  
    CAST(v.properties->>'total_points' AS REAL) / CAST(v.properties->>'number_of_game' AS REAL) AS avg_pts  
FROM  
    vertices v  
JOIN edges e  
    ON e.subject_identifier = v.identifier  
    AND e.subject_type = v.type  
WHERE e.object_type = 'player'::vertex_type;
```

## Lab

Setting up Postgres using Docker

#### Run Postgres in Docker**


- Install Docker Desktop from **[here](https://www.docker.com/products/docker-desktop/)**.
    
- Copy **`example.env`** to **`.env`**:
    
    ```shell
    cp example.env .env
    ```
    
- Start the Docker Compose container:
        
        ```shell
        make up
        ```
- When you're finished with your Postgres instance, you can stop the Docker Compose containers with:

        ```shell
        make down
        ```
  
####  Additional Docker Make commands

- To restart the Postgres instance, you can run **`make restart`**.
- To see logs from the Postgres container, run **`make logs`**.
- To inspect the Postgres container, run **`make inspect`**.
- To find the port Postgres is running on, run **`make ip`**.
