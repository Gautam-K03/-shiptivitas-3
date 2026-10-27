-- TYPE YOUR SQL QUERY BELOW

-- PART 1: Create a SQL query that maps out the daily average users before and after the feature change

-- Average daily active users before vs after the Kanban release (2018-06-02)
WITH daily AS (
  SELECT date(login_timestamp, 'unixepoch') AS day,
         COUNT(DISTINCT user_id)            AS dau
  FROM login_history
  GROUP BY day
)
SELECT CASE WHEN day < '2018-06-02' THEN 'before' ELSE 'after' END AS period,
       ROUND(AVG(dau), 2) AS avg_dau,
       COUNT(*)           AS days_in_period
FROM daily
GROUP BY period;

-- Daily active users for every day (data for the line chart)
SELECT date(login_timestamp, 'unixepoch') AS day,
       COUNT(DISTINCT user_id)            AS daily_active_users
FROM login_history
GROUP BY day
ORDER BY day;


-- PART 2: Create a SQL query that indicates the number of status changes by card

-- Only real status changes are counted (reordering inside a lane is excluded)
SELECT c.id,
       c.name,
       COUNT(*) AS status_changes
FROM card_change_history h
JOIN card c ON c.id = h.cardId
WHERE h.oldStatus <> h.newStatus
GROUP BY c.id, c.name
ORDER BY status_changes DESC;