---
layout: post
title: Time Series with PostgreSQL
subtitle: Poots Persuades Postgres to Populate Points
---

Another celebration was called for when I finally managed to coerce PostgreSQL into supplying missing dates while querying some time series data. 

The point (intended) of time series data is to measure data at successive points in time spaced at uniform time intervals. However, when there is no data for a given interval, gaps start to appear. I happened to be querying how many users had been registering on a daily basis:

    SELECT
      date_trunc('day', users.created_at) AS day, 
      count(users.id) AS count 
    FROM
      users 
    WHERE
      created_at >= '2013-01-01 00:00'
      AND created_at < '2013-01-08 00:00'
    GROUP BY
      day
    ORDER BY
      day ASC

I’m using the PostgreSQL function `date_trunc()` which truncates a timestamp (`users.created_at`) to the specified precision (day). The results aren’t exactly what I need though; it wasn’t so busy in the first week of January and we have some gaps.

    +---------------------+-------+
    | day                 | count |
    +---------------------+-------+
    | 2013-01-02 00:00:00 | 1     |
    | 2013-01-03 00:00:00 | 3     |
    | 2013-01-07 00:00:00 | 3     |
    +---------------------+-------+

I wanted to be looking at this instead, the gaps being filled:

    +---------------------+-------+
    | day                 | count |
    +---------------------+-------+
    | 2013-01-01 00:00:00 | 0     |
    | 2013-01-02 00:00:00 | 1     |
    | 2013-01-03 00:00:00 | 3     |
    | 2013-01-04 00:00:00 | 0     |
    | 2013-01-05 00:00:00 | 0     |
    | 2013-01-06 00:00:00 | 0     |
    | 2013-01-07 00:00:00 | 3     |
    +---------------------+-------+

Now, there is more than one way to skin a cat. You might be tempted to solve this problem in your application code, assuming a greater familiarity with your language of choice than SQL. I was, but then I had UTC+1 problems. After the first attempt I gave up in exasperation. More importantly though, it smelled suspiciously superflous; like something my database should be doing for me.

A bit of documentation diving later I surfaced with the `generate_series()` function. When passed a couple of timestamps and a step interval it gives us the desired list of dates:

    SELECT
      * 
    FROM
      generate_series(
        '2013-01-01 00:00'::timestamp,
        '2013-01-07 00:00'::timestamp,
        '1 day') AS date
    
    +---------------------+
    | date                |
    +---------------------+
    | 2013-01-01 00:00:00 |
    | 2013-01-02 00:00:00 |
    | 2013-01-03 00:00:00 |
    | 2013-01-04 00:00:00 |
    | 2013-01-05 00:00:00 |
    | 2013-01-06 00:00:00 |
    | 2013-01-07 00:00:00 |
    +---------------------+

I must confess I reached out to Alex Kavanagh ([@ajkavanagh][alex]) at this stage to help me put two and two together. Thanks to Alex, I can tell you the final solution involves joining the two results tables—Rails must have spoiled my once less than formidable knowledge of SQL joins. This case requires a `LEFT OUTER JOIN` to ensure records without any data in the corresponding table are included, otherwise we’d be back at square one. It also means the time series result set needs to come first.

    SELECT
      date,
      coalesce(count,0) AS count
    FROM
     generate_series(
       '2013-01-01 00:00'::timestamp,
       '2013-01-07 00:00'::timestamp,
       '1 day') AS date
    LEFT OUTER JOIN
      (SELECT
         date_trunc('day', users.created_at) as day,
         count(users.id) as count
       FROM users
       WHERE
         created_at >= '2013-01-01 00:00'
         AND created_at < '2013-01-08 00:00'
         GROUP BY day) results
    ON (date = results.day) 

There are two minor changes to note. The first is I’m using the `coalesce()` function to convert `null` to `0`. Secondly, we can omit the ordering on the results table since the generated time series is ordered by default. It does the job perfectly:

    +---------------------+-------+
    | date                | count |
    +---------------------+-------+
    | 2013-01-01 00:00:00 | 0     |
    | 2013-01-02 00:00:00 | 1     |
    | 2013-01-03 00:00:00 | 3     |
    | 2013-01-04 00:00:00 | 0     |
    | 2013-01-05 00:00:00 | 0     |
    | 2013-01-06 00:00:00 | 0     |
    | 2013-01-07 00:00:00 | 3     |
    +---------------------+-------+


I’m glad I persevered with PostgreSQL in this scenario. It feels a lot cleaner to solve the problem at the source rather than employing verbose and brittle application code.

—*1705, Sunday 15th December 2013.*

<div class="references">
  <h3>References</h3>
  <ul>
    <li><a href="http://www.postgresql.org/docs/9.3/static/functions-datetime.html#FUNCTIONS-DATETIME-TRUNC"><code>date_trunc()</code></a></li>
    <li><a href="http://www.postgresql.org/docs/9.3/static/functions-srf.html"><code>generate_series()</code></a></li>
    <li><a href="http://www.postgresql.org/docs/9.3/static/functions-conditional.html#FUNCTIONS-COALESCE-NVL-IFNULL"><code>coalesce()</code></a></li>
  </ul>
</div>

[alex]: http://twitter.com/ajkavanagh
