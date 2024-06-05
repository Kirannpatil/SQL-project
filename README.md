# SQL-project

![Description](https://github.com/Kirannpatil/SQL-project/assets/141953021/bc08ad13-aee3-4487-bdde-e794f4455012)


  ```sql
  CREATE DATABASE pizza_runner;
  USE pizza_runner;

 CREATE TABLE runners (
  runner_id INTEGER,
  registration_date DATE
);
```

  ```sql
  INSERT INTO runners
  (runner_id, registration_date)
   VALUES
  (1, '2021-01-01'),
  (2, '2021-01-03'),
  (3, '2021-01-08'),
  (4, '2021-01-15');
```
```sql
  CREATE TABLE customer_orders (
  order_id INTEGER,
  customer_id INTEGER,
  pizza_id INTEGER,
  exclusions VARCHAR(4),
  extras VARCHAR(4),
  order_time DATETIME
);
```
```sql
INSERT INTO customer_orders
  (order_id, customer_id, pizza_id, exclusions, extras, order_time)
VALUES
  ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
  ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
  ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
  ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
  ('5', '104', '1', NULL, '1', '2020-01-08 21:00:29'),
  ('6', '101', '2', NULL, NULL, '2020-01-08 21:03:13'),
  ('7', '105', '2', NULL, '1', '2020-01-08 21:20:29'),
  ('8', '102', '1', NULL, NULL, '2020-01-09 23:54:33'),
  ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
  ('10', '104', '1', NULL, NULL, '2020-01-11 18:34:49'),
  ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');
  ```
```sql
  CREATE TABLE runner_orders (
  order_id INTEGER,
  runner_id INTEGER,
  pickup_time VARCHAR(19),
  distance VARCHAR(7),
  duration VARCHAR(10),
  cancellation VARCHAR(23)
);
```
  ```sql
  INSERT INTO runner_orders
  (order_id, runner_id, pickup_time, distance, duration, cancellation)
VALUES
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
  ('6', '3', NULL, NULL, NULL, 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', NULL),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', NULL),
  ('9', '2', NULL, NULL, NULL, 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', NULL);
```

```sql
  CREATE TABLE pizza_names (
  pizza_id INTEGER,
  pizza_name TEXT
  );

  INSERT INTO pizza_names
  (pizza_id, pizza_name)
   VALUES
  (1, 'Meatlovers'),
  (2, 'Vegetarian');
```

```sql
  CREATE TABLE pizza_recipes (
  pizza_id INTEGER,
  toppings TEXT
  )
  ;

  INSERT INTO pizza_recipes
  (pizza_id, toppings)
   VALUES
  (1, '1, 2, 3, 4, 5, 6, 8, 10'),
  (2, '4, 6, 7, 9, 11, 12')
  ;
```
```sql
  CREATE TABLE pizza_toppings (
  topping_id INTEGER,
  topping_name TEXT
   );

   INSERT INTO pizza_toppings
  (topping_id, topping_name)
   VALUES
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');
```
 

-- cleaninng

```sql
CREATE TEMPORARY TABLE customer_orders_temp AS
SELECT 
  order_id, 
  customer_id, 
  pizza_id, 
  CASE 
    WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN ' '
    ELSE exclusions
  END AS exclusions,
  CASE 
    WHEN extras IS NULL OR extras LIKE 'null' THEN ' '
    ELSE extras 
  END AS extras, 
  order_time
FROM customer_orders;
```
select * from customer_orders_temp;

```sql
CREATE TEMPORARY TABLE runner_orders_temp AS
SELECT 
  order_id, 
  runner_id,
  CASE 
    WHEN pickup_time LIKE 'null' THEN ' '
    ELSE pickup_time 
  END AS pickup_time,
  CASE 
    WHEN distance LIKE 'null' THEN ' '
    WHEN distance LIKE '%km' THEN TRIM('km' FROM distance) 
    ELSE distance 
  END AS distance,
  CASE 
    WHEN duration LIKE 'null' THEN ' ' 
    WHEN duration LIKE '%mins' THEN TRIM('mins' FROM duration) 
    WHEN duration LIKE '%minute' THEN TRIM('minute' FROM duration) 
    WHEN duration LIKE '%minutes' THEN TRIM('minutes' FROM duration)       
    ELSE duration 
  END AS duration,
  CASE 
    WHEN cancellation IS NULL OR cancellation LIKE 'null' THEN ''
    ELSE cancellation 
  END AS cancellation
FROM runner_orders;
```
select * from runner_orders_temp;

-- QUESTIONS-------------------------------------

```sql
-- How many pizzas were ordered?
SELECT COUNT(*) AS pizza_order_count
FROM customer_orders_temp;

-- How many unique customer orders were made?
SELECT COUNT(DISTINCT order_id) AS unique_customer_count
FROM customer_orders_temp;

-- How many successful orders were delivered by each runner?
SELECT runner_id, COUNT(order_id) AS successful_order_count
FROM runner_orders_temp 
WHERE distance != 0
GROUP BY runner_id;

-- How many of each type of pizza was delivered?
SELECT C.pizza_id,pizza_name, COUNT(*) AS pizza_count
FROM customer_orders_temp AS C  
JOIN runner_orders_temp AS R
ON C.order_id= R.order_id
JOIN pizza_names AS P
ON C.pizza_id = P.pizza_id
WHERE distance != 0
GROUP BY C.pizza_id,pizza_name;

-- How many Vegetarian and Meatlovers pizza were ordered by each customer?
SELECT customer_id, pizza_name,COUNT(*) AS pizza_count
FROM customer_orders_temp AS C
JOIN pizza_names AS P
ON C.pizza_id=P.pizza_id
GROUP BY customer_id,pizza_name
ORDER BY customer_id ASC;

-- What was the maximum number of pizzas delivered in a single order?
WITH cte AS 
 (
   SELECT cust.order_id,COUNT(cust.pizza_id) AS pizza_per_order
   FROM customer_orders_temp AS cust
   JOIN runner_orders_temp AS run
   ON cust.order_id = run.order_id
   GROUP BY cust.order_id
 )
  SELECT MAX(pizza_per_order) AS pizza_count 
  FROM cte;
  
  -- For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
   SELECT c.customer_id,
  SUM(
      CASE WHEN c.exclusions != ' ' OR c.extras != ' ' THEN 1 ELSE 0 END 
	  ) AS change_1,
  SUM(
      CASE WHEN c.exclusions = ' ' AND c.extras = ' ' THEN 1 ELSE 0 END
	  ) AS no_change
    FROM customer_orders_temp AS c
    JOIN runner_orders_temp AS r
	ON c.order_id = r.order_id
    WHERE r.distance != 0
    GROUP BY c.customer_id
    ORDER BY c.customer_id;
    
    
    -- How many pizzas were delivered that had both exclusions and extras?
    SELECT COUNT(*) AS pizza_having_exclusions_n_extras
    FROM customer_orders_temp AS c
    JOIN runner_orders_temp AS r
	ON c.order_id = r.order_id
    WHERE r.distance!=0 
    AND exclusions != ' ' 
	AND extras != ' ';
    
    -- What was the total volume of pizzas ordered for each hour of the day? 
SELECT 
HOUR(order_time) AS hours,
COUNT(order_id) AS "pizza ordered"
FROM customer_orders_temp
GROUP BY hours
ORDER BY hours;

-- How many runners signed up for each 1 week period?
SELECT 
YEAR(registration_date) AS year,
WEEK(registration_date) AS week,
COUNT(runner_id) AS runners_signed_up
FROM runners
GROUP BY year, week
ORDER BY year, week;


-- What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
 WITH time_taken AS
  (
    SELECT c.order_id,r.runner_id,
	c.order_time,r.pickup_time,
	 TIMESTAMPDIFF(minute,c.order_time,r.pickup_time) AS pickup_minutes
	 FROM customer_orders_temp AS c
	 JOIN runner_orders_temp AS r
	 ON c.order_id = r. order_id
	 WHERE r.distance != 0
	 GROUP BY r.runner_id,c.order_id,c.order_time,r.pickup_time
  )
    SELECT runner_id, AVG(pickup_minutes) AS pickup_avg_minutes
	FROM time_taken
	GROUP BY runner_id;

-- Is there any relationship between the number of pizzas and how long the order takes to prepare?
 WITH prepare_time AS
  (
    SELECT c.order_id,
	 COUNT(c.order_id) AS pizza_order,
	 c.order_time,r.pickup_time,
	 TIMESTAMPDIFF(minute,c.order_time,r.pickup_time) AS prepare_time_min
	 FROM customer_orders_temp AS c
	 JOIN runner_orders_temp AS r
	 ON c.order_id = r. order_id
	 WHERE r.distance != 0
	 GROUP BY c.order_id,c.order_time,r.pickup_time
   )
	SELECT pizza_order, AVG(prepare_time_min) AS avg_prepare_time_min
	FROM prepare_time
	GROUP BY pizza_order;
    
    -- What was the average distance travelled for each customer?
    SELECT customer_id, AVG(distance) AS avg_distance
	FROM customer_orders_temp AS c
	JOIN runner_orders_temp AS r
	 ON c.order_id= r.order_id
	WHERE distance != 0
	GROUP BY customer_id;	
    
    --  What was the average speed for each runner for each delivery and do you notice any trend for these values??
   SELECT runner_id,order_id,ROUND(AVG(60*(distance/duration)),1) AS speed
   FROM runner_orders_temp
   WHERE distance!=0
   GROUP BY runner_id,order_id
   ORDER BY runner_id;
   
   -- What was the difference between the longest and shortest delivery times for all orders? 
   SELECT MAX(duration) - MIN(duration) as delivery_difference_time
   FROM runner_orders_temp
   WHERE distance != 0;
   
   
-- What is the successful delivery percentage for each runner?
SELECT runner_id, 
   ROUND(100 * SUM(CASE WHEN distance = 0 THEN 0 ELSE 1 END) / COUNT(*), 0) AS successful_percent
   FROM runner_orders_temp
   GROUP BY runner_id;
   ```









