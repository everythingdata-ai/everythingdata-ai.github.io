---
layout: post
title: Dimensional Data Modeling - Data Expert Bootcamp
categories: [Data Engineering, Data Modeling]
---

Today I continued the Zach Wilson Data Engineering Bootcamp.

The second week is about Fact Data Modeling.

To continue on my previous post, here are my notes from the second week of the bootcamp.

## Fact Data Modeling Fundamentals

### Introduction
Fact data is the biggest data you'll work with, it's much bigger (usually 10-100x) than dimensions 
It's every event that a user does
It's important to be careful when modeling fact data, because when poorly modeled it can drive the cost exponentially

### What is a fact ?
Think of it as something that happened : 
- A user logs in to an app
- A transaction is made
- You run a mile with your smartwatch
Facts are not slowly changing, which makes them easier to model than dimensions in some respects.

### How does fact modeling work ?
Normalization vs Denormalization :
- Noramalized : don't have any dimensional attributes, just IDs to join to get that information
- Denormalized : bring in some dimensional attributes for quicker analysis at the cost of more storage

Fact data and raw logs are not the same :
- Raw logs : ugly schemas designed for online systems, potentially contains duplicates and quality errors, usually have shorter retention
- Fact data : nice column names, quality guarantees like uniqueness/not null/... , usually have longer retentition

Who, What, Where, When, How ?
- Who fields are usually pushed out as IDs (this user clicked this button)
- Where fields can be modeled with IDs, but more likely bring in dimensions, especially if they're high cardinality like "device_id"
- How fields are very similar to "Where" (He used an iPhone to make this click)
- What fields are fundamentally part of the nature of the fact (SENT, GENERATED, CLICKED, DELIVERED...)
- When fields are mostly "event_timestamp" of "event_date"

### How does logging fit into fact data ?
Logging brings in all the critical context for your fact data
Don't log everything, only what you really need
Logging should conform to values specified by the online teams (Apache Thrift)

### Potential options when working with high volume
- Sampling : doesn't work for all use cases, works best for metric-driven use-cases where imprecision isn't an issue
- Bucketing : fact data can be bucketed by one of the important dimensions (usually user). Bucket joins can be much faster than shuffle joins. Sorted-merge Bucket (SMB) joins can do joins without Shuffle at all
## How long should you hold onto fact data ?
High volumes make fact data much more costly to hold onto for a long time
Big tech had an interesting approach : 
- Any fact tables < 10 TBs, retention didn't matter much
- Anonymisation of facts usually happened after 60-90 days, the data would be moved to a new table with the PII stripped
- Any fact tables > 100 TBs, short retention (14 days of less)
## Deduplication of fact data
Facts can often be duplicated : you can click a notification multiple times
Intraday deduping options : 
- Streaming : allows you to capture most duplicates in a very efficient manner, 15 minute to hourly windows are a sweet spot 
- Microbatch
Example : **[Microbatch Hourly Deduped Tutorial]([[https://github.com/EcZachly/microbatch-hourly-deduped-tutorial]] )**

### Use case : NBA games

```sql
CREATE TABLE fct_game_details (  
    dim_game_date DATE,  
    dim_season INTEGER,  
    dim_team_id INTEGER,  
    dim_player_id INTEGER,  
    dim_player_name TEXT,  
    dim_start_position TEXT,  
    dim_is_playing_at_home BOOLEAN,  
    dim_did_not_play BOOLEAN,  
    dim_did_not_dress BOOLEAN,  
    dim_not_with_team BOOLEAN,  
    m_minutes REAL, --m stands for measure  
    m_fgm INTEGER,  
    m_fga INTEGER,  
    m_fg3m INTEGER,  
    m_fg3a INTEGER,  
    m_ftm INTEGER,  
    m_fta INTEGER,  
    m_oreb INTEGER,  
    m_dreb INTEGER,  
    m_reb INTEGER,  
    m_ast INTEGER,  
    m_stl INTEGER,  
    m_blk INTEGER,  
    m_turnovers INTEGER,  
    m_pf INTEGER,  
    m_pts INTEGER,  
    m_plus_minus INTEGER,  
    PRIMARY KEY (dim_game_date, dim_player_id, dim_team_id)  
);  
  
INSERT INTO fct_game_details  
WITH deduped AS (  
    SELECT g.game_date_est,  
           g.season,  
           g.home_team_id,  
        gd.*, row_number() over (partition by gd.game_id, team_id, player_id ORDER BY g.game_date_est) as row_num  
    FROM game_details gd  
    JOIN games g ON gd.game_id = g.game_id  
)  
SELECT  
    game_date_est,  
    season,  
    team_id,  
    player_id,  
    player_name,  
    start_position,  
    team_id = home_team_id AS dim_is_playing_at_home,  
    COALESCE(position('DNP' in comment), 0) > 0 as dim_did_not_play,  
    COALESCE(position('DND' in comment), 0) > 0 as dim_did_not_dress,  
    COALESCE(position('NWT' in comment), 0) > 0 as dim_not_with_team,  
    CAST(split_part(min, ':', 1) AS REAL) + CAST(split_part(min, ':', 2) AS REAL)/60 as minutes,  
    fgm,  
    fga,  
    fg3m,  
    fg3a,  
    ftm,  
    fta,  
    oreb,  
    dreb,  
    reb,  
    ast,  
    stl,  
    blk,  
    "TO" AS turnovers,  
    pf,  
    pts,  
    plus_minus  
FROM deduped  
WHERE row_num = 1;
```

## The blurry line between Fact and Dim

### Is it a Fact or a Dimension ?
- Did a user login today ? The log in event would be a fact that informs the "dim_is_active" dimension VS the stat "dim_is_activated" which is something that is state-driven, not activity driven
- You can aggregate facts and turn them into dimensions : is this person a "high engager" or "low engager" ? CASE WHEN to bucketize aggregated facts can be very useful to reduce the cardinality (5 to 10 buckets is the sweet spot). A goog way is to slice by percentiles (0 to 20, 20 to 40...)

### Properties of Facts vs Dimensions
Dimensions : 
- Usually show in GROUP BY when doing analytics
- Can be "high cardinality" or "low cardinality"
- Generally come from a snapshot of state

Facts :
- Usually aggregated when doing analytics by things like SUM, AVG, COUNT...
- Almost always higher volume than dimensions, although some fact sources are low-volume, think "rare events"
- Generally come from events and logs

### Airbnb example
Is the price of a night on Airbnb a fact or dimension ?
The host can set the price, which sounds like an event
It can easily be SUM, AVG, COUNT'd like regular facts
Prices on Airbnb are double, therefore extremely high carinality

Despite all of this, the price is modeled as a dimension at Airbnb, it's an attribute (state) of a night

The fact in this case would be the host changing the setting that impacted the price

### Boolean/Existence-based Fact/Dimensions
- dim_is_active, dim_bought_something, etc : these are usually on the daily/hour grain
- dim_has_ever_booked, dim_ever_active, dim_ever_labeled_fake : these "ever" dimensions look to see if there has "ever" been a log, and once it flips one way, it never goes back. It's an interesting, simple and powerful feature for machine learning. For example, an Airbnb host with active listings who has never been booked, looks sketchier over time
- days_since_last_active, days_since_signup... : Very common in retention analytical patterns (lookup J curves for more details)

### Categorical Fact/Dimensions
Scoring class : A dimension that is derived from fact data. Example : Good, average, bad
Often calculated with CASE WHEN logic and "bucketizing". Example : Airbnb superhost

### The extremely efficient Datelist INT data structure
A Datelist Int is a data structure that encodes multiple days of user activity in a single integer value (usually a BIGINT)

Imagine a cumulated schema like 'user_cumulated' with a column dates_active which is an array of all the recent days that a user was active
You can turn that into a structure like datelist_int = 1000011 where each number represents a day of the week, and 1 being active and 0 inactive 
Extremely efficient way to manage user growth
 [Max Sung's explanation](https://www.linkedin.com/pulse/datelist-int-efficient-data-structure-user-growth-max-sung/)
### Lab : track user activity 
```sql
CREATE TABLE users_cumulated (  
    user_id TEXT,  
    -- The list of date in the past where the user was active  
    dates_active DATE[],  
    -- The current date for the user  
    date DATE,  
    PRIMARY KEY (user_id, date)  
);  
  
INSERT INTO users_cumulated  
WITH yesterday AS(  
    SELECT *  
    FROM users_cumulated  
    WHERE date = DATE('2023-01-30')  
),  
    today AS(  
    SELECT  
        CAST(user_id AS TEXT),  
        DATE(CAST(event_time AS TIMESTAMP)) AS date_active  
    FROM events  
    WHERE DATE(CAST(event_time AS TIMESTAMP)) = DATE('2023-01-31')  
    AND user_id IS NOT NULL  
    GROUP BY user_id, DATE(CAST(event_time AS TIMESTAMP))  
    )  
SELECT  
    COALESCE(t.user_id, y.user_id) AS user_id,  
    CASE WHEN y.dates_active IS NULL THEN ARRAY[t.date_active]  
        WHEN t.date_active IS NULL THEN y.dates_active  
        ELSE ARRAY[t.date_active] || y.dates_active  
        END  
        as dates_active,  
    COALESCE(t.date_active, y.date + Interval '1 day') AS date  
FROM today t  
FULL OUTER JOIN yesterday y  
ON t.user_id = y.user_id;  
  
  
-- Turn the dates_active from an array into a date list of 30 days  
WITH users AS (  
    SELECT * FROM users_cumulated  
    WHERE date = DATE('2023-01-31')  
),  
    series AS (  
        SELECT * FROM  
             generate_series(DATE('2023-01-01'), DATE('2023-01-31'), INTERVAL '1 day') as series_date  
    ),  
    place_holder_ints AS (SELECT CASE WHEN  
        dates_active @> ARRAY [DATE(series_date)]  
        --  
        THEN CAST(POW(2, 32 - (date - DATE(series_date))) AS BIGINT)  
        ELSE 0  
        END as placeholder_int_value,  
                                 *  
                          FROM users  
                                   CROSS JOIN series)  
SELECT  
    user_id,  
    CAST(CAST(SUM(placeholder_int_value) AS BIGINT) AS BIT(32)),  
    BIT_COUNT(CAST(CAST(SUM(placeholder_int_value) AS BIGINT) AS BIT(32))) > 0 AS dim_is_monthly_active,  
    BIT_COUNT(CAST('11111110000000000000000000000000' AS BIT(32)) & CAST(CAST(SUM(placeholder_int_value) AS BIGINT) AS BIT(32))) > 0 AS dim_is_weekly_active,  
    BIT_COUNT(CAST('10000000000000000000000000000000' AS BIT(32)) & CAST(CAST(SUM(placeholder_int_value) AS BIGINT) AS BIT(32))) > 0 AS dim_is_daily_active  
FROM place_holder_ints  
GROUP BY user_id  
;
```
