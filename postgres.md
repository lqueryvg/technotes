# PostgreSQL

https://platform.stratascratch.com/

## Performance

### prepared statements

```sql
PREPARE insert_task AS
  INSERT INTO task (name, due_date, is_done)
  VALUES ($1, $2, $3);
EXECUTE insert_task ('rotate keys', '2023-05-01', false);
```

- maybe save 20%
- only for 1 session; clients can't share
- good for long sessions executing a lot of repeated queries

### explain

```sql
EXPLAIN ANALYZE SELECT * from users where age > 21;
```
- watch out for "Seq Scan"

### indexes

- avoid full scan; gives direct access to rows
- slows write performance

### partition

- improves write performance
- can make scans slower
- need to manage creation of new partitions with pg_partman or pg_cron

```sql
CREATE TABLE events (...)
PARTITION BY RANGE (event_timestamp)
CREATE TABLE events_xxx PARTITION OF events FOR VALUE FROM (...) TO (...)
CREATE TABLE events_yyy PARTITION OF events FOR VALUE FROM (...) TO (...)
CREATE TABLE events_zzz PARTITION OF events FOR VALUE FROM (...) TO (...)
```

### COPY 

```sql
COPY events from STDIN
COPY events FROM 'data.csv'
```
- can support csv, text or bin
- maybe 10x faster than inserts
- whole operation fails if data malformed
- can only copy from client via STDIN (file must be on server)

### read replica

## JOIN

default is `INNER`

## misc

```sql
SELECT version();
SELECT DISTINCT ...
SELECT * FROM emp LIMIT 1;
DATE_PART('MONTH', time_id)
EXTRACT(MONTH FROM date_sold) BETWEEN 1 AND 6  -- jan to june inclusive
to_char(shipment_date, 'YYYY-MM') AS year_month,
count(distinct (shipment_id, sub_id))
avg(bedrooms) as n_bedrooms_avg

WITH max_profits AS  
  (SELECT MAX(profits) AS max_profit  
   FROM forbes_global_2010_2014  
   WHERE sector = 'Financials')  
SELECT company, continent
FROM forbes_global_2010_2014
JOIN max_profits
  ON forbes_global_2010_2014.profits = max_profits.max_profit  

-- all customers even if they have no order
SELECT first_name, last_name, city, o.order_details
FROM customers AS c
LEFT JOIN orders o ON c.id = o.cust_id
ORDER BY first_name, o.order_details ASC
WHERE first_name IN ('Jill', 'Eva')

WITH dept_avg AS (
  SELECT
    department,
    avg(salary) as avg_salary
  FROM employee
  GROUP BY department
)
SELECT e.department, e.first_name, salary, d.avg_salary
FROM employee e
JOIN dept_avg d
  ON e.department = d.department
ORDER BY e.department

select * from lyft_drivers
WHERE yearly_salary < 30000 OR yearly_salary >= 70000;

WITH loaction_popularities AS (
    SELECT e.location, h.popularity FROM facebook_employees AS e
    JOIN facebook_hack_survey AS h ON e.id = h.employee_id
)
SELECT location, AVG(popularity) as avg_popularity FROM loaction_popularities
GROUP BY location

SELECT DISTINCT p.* FROM facebook_posts AS p
JOIN facebook_reactions AS r
ON p.post_id = r.post_id
  AND r.reaction = 'heart';


SELECT id, first_name,	last_name,	department_id,	max(salary)
FROM ms_employee_salary
GROUP BY id, first_name, last_name, department_id
ORDER BY id
;
```

"SARGABLE" = "Search ARGument ABLE" means queries which can use indexes for faster execution


## Don't Do This

https://wiki.postgresql.org/wiki/Don't_Do_This

- Database Encoding
  - Don't use SQL_ASCII
- Tool usage
  - Don't use psql -W or --password
  - Don't use rules
  - Don't use table inheritance
- SQL constructs
  - Don't use NOT IN
  - Don't use upper case table or column names
  - Don't use BETWEEN (especially with timestamps)
- Date/Time storage
  - Don't use timestamp (without time zone)
  - Don't use timestamp (without time zone) to store UTC times
  - Don't use timetz
  - Don't use CURRENT_TIME
  - Don't use timestamp(0) or timestamptz(0)
  - Don’t use +/-HH:mm as a text Time Zone name
- Text storage
  - Don't use char(n)
  - Don't use char(n) even for fixed-length identifiers
  - Don't use varchar(n) by default
- Other data types
  - Don't use money
  - Don't use serial
- Authentication
  - Don't use trust authentication over TCP/IP (host, hostssl)

