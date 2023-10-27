## ⭐ Conceptual Background ⭐
<div align=justify> Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well, according to Wikipedia anyway…) 
  
Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

All datasets exist within the pizza_runner database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions. </div>

## ☔ Data schema ☔
![Pizza Runner](https://github.com/cawcs/8-Week-SQL-Challenge/assets/147532517/55ecba30-e5e8-450f-ac56-c12e9cf5ec9c)

## 🐱Questions and Solutions 🐶
### Section A: Pizza Metrics 🍕
### ***1. How many pizzas were ordered?***
````
select
  count(order_id) as pizza_ordered
from
  customer_orders_temp
````
***Answer***
|pizza_ordered|
|-|
|14|

### ***2. How many unique customer orders were made?***
````
select
  count(distinct order_id) as unique_order
from
  customer_orders_temp
````
***Answer***
|unique_order|
|-|
|10|

### ***3. How many successful orders were delivered by each runner?***
````
select
  runner_id,
  count(order_id) as successful_order
from
  runner_orders_temp
where
  cancellation is null
group by
  runner_id
````
***Answer***
|runner_id|	successful_order|
|-|-|
|1	|4|
|2	|3|
|3	|1|

### ***4. How many of each type of pizza was delivered?***
````
select
  pizza_name,
  count(cot.order_id) as type_pizza_delivered
from
  customer_orders_temp as cot
join
  runner_orders_temp as rot on rot.order_id = cot.order_id
join
  pizza_names as pn on pn.pizza_id = cot.pizza_id
where
  cancellation is null
group by
  pizza_name
````
***Answer***
|pizza_name	|type_pizza_delivered|
|-|-|
|Meatlovers|	9|
|Vegetarian|	3|

### ***5. How many Vegetarian and Meatlovers were ordered by each customer?***
````
select
  customer_id,
  pizza_name,
  count(pizza_name) as ordered_by_customer
from
  pizza_names as pn
join
  customer_orders_temp as cot on cot.pizza_id = pn.pizza_id
group by
  pizza_name,
  customer_id
````
|customer_id|	pizza_name	|ordered_by_customer|
|-|-|-|
|101	|Meatlovers|	2|
|102	|Meatlovers	|2|
|102	|Vegetarian|	1|
|103	|Meatlovers	|3|
|103	|Vegetarian	|1|
|104	|Meatlovers|	3|
|101	|Vegetarian|	1|
|105	|Vegetarian|	1|

### ***6. What was the maximum number of pizzas delivered in a single order?***
````
select
  order_id,
  count(pizza_id) as max_pizza_ordered
from
  customer_orders_temp
group by
  order_id
````
***Answer***
|order_id	|max_pizza_ordered|
|-|-|
|1	|1|
|2|	1|
|3	|2|
|4	|3|
|5	|1|
|6|	1|
|7|	1|
|8|	1|
|9|	1|
|10|	2|

### ***7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?***
````
select
  customer_id,
  sum(
    case when exclusions <> '' or extras <> '' then 1
    else 0
    end) as with_change,
  sum(
    case when exclusions = '' or extras = '' then 1
    else 0
    end) as no_change
from
  customer_orders_temp as cot
join
  runner_orders_temp as rot on rot.order_id = cot.order_id
where
  cancellation is null
group by
  customer_id
````
***Answer***
|customer_id	|with_change|	no_change|
|-|-|-|
|101|	0	|2|
|102|	0	|2|
|103	|3|	3|
|104	|1|	0|
|105	|0	|0|

### ***8. How many pizzas were delivered that had both exclusions and extras?***
````
select
  count(*) as pizza_had_exclusions_extras
from
  customer_orders_temp as co
join
  runner_orders_tempt as rot on cot.order_id = rot.order_id
where rot.cancellation is null
and exclusions <> ''
and extras <> ''
````
***Answer***
|pizza_had_exclusions_extras|
|-|
|1|

### ***9. What was the total volume of pizzas ordered for each hour of the day?***
````
select
  hour(order_time) as hour_of_day,
  count(*) as num_pizzas_ordered
from
  customer_orders
group by
  hour_of_day
````
***Answer***
|hour_of_day|	num_pizzas_ordered|
|-|-|
|18	|3|
|19|	1|
|23|	3|
|13|	3|
|21	|3|
|11	|1|

### ***10. What was the volume of orders for each day of the week?***
````
select 
  dayname(order_time) as day, 
  count(*) as ordered_pizzas 
from 
  customer_orders_temp 
group by 
  dayname(order_time)
````
***Answer***
|day	|ordered_pizzas|
|-|-|
|Wednesday	|5|
|Thursday	|3|
|Saturday	|5|
|Friday	|1|

--------------------------------------- 🥘Take a break 🧋---------------------------------------
