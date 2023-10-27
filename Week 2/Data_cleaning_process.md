## Data Overview                                                                                                                                                                                     
![Pizza Runner](https://github.com/cawcs/8-Week-SQL-Challenge/assets/147532517/8871052e-c0c8-4297-8d7a-33536e3d13ea)

### 1. runner_orders table                                                                                                                                                                             
```
select * from runner_orders
```

|order_id	|runner_id|	pickup_time|	distance|	duration|	cancellation
|-|-|-|-|-|-|
|1|	1|	2020-01-01 |18:15:34	|20km|	32 minutes|	
|2	|1|	2020-01-01 |19:10:54	|20km|	27 minutes	|
|3	|1|	2020-01-03 |00:12:37|	13.4km	|20 mins|	None|
|4	|2	|2020-01-04 |13:53:03	|23.4|	40|	None|
|5	|3	|2020-01-08| 21:10:57	|10|	15|	None|
|6|	3|	null|	null|	null|	Restaurant| Cancellation|
|7	|2|	2020-01-08| 21:30:45|	25km|	25mins	|null|
|8	|2|	2020-01-10 |00:15:02	|23.4 km|	15 minute	|null|
|9|	2	|null	|null	|null|	Customer |Cancellation|
|10	|1	|2020-01-11| 18:50:20|	10km|	10minutes|	null|

***Data status***
>  - There are missing data/blank spaces and null values in `pickup_time`, `cancellation` columns 
>  - `pickup_time`, `distance`, and `duration` in rows 6 and 9 have null values 
>  - Inconsistent values in `distance`, `duration`

***Cleaning process***
> - With column `pickup_time`, remove `null` and replace it with a blank space
> - With column `distance`, remove `null` with blank space and remove `km`
> - With column `duration`, remove `null` with blank space and remove all units of measurement (i.e. `minutes`, `mins`, `minute`)
> - With column `cancellation`, remove all `null` values with a blank space
````
create temporary table runner_orders_temp as
select
  order_id,
  runner_id,
case
  when pickup_time like 'null' then null
  else pickup_time
  end as pickup_time,
case
  when distance = 'null' then null
  else regexp_replace(distance, '[a-zA-z]+', '')
  else distance
  end as distance,
case
  when duration = 'null' then null
  else regexp_replace(duration, '[a-zA-Z]+', '')
  end as duration,
case
  when cancellation = '' then null
  when cancellation = "null" then null
  else cancellation
  end as cancellation
from runner_orders
````
> - Modify the data type
````
alter table runner_orders_temp
modify column pickup_time datetime,
modify column distance FLOAT,
modify column duration INT;
````
***Cleaned runner_orders_temp table***
|order_id	|runner_id|	pickup_time|	distance	|duration	|cancellation|
|-|-|-|-|-|-|
|1|1|	2020-01-01 18:15:34|	20.0	|32	|None|
|2	|1	|2020-01-01 19:10:54|	20.0|	27|	None|
|3|	1	|2020-01-03 00:12:37	|13.4|	20|	None|
|4	|2	|2020-01-04 13:53:03|	23.4|	40|	None|
|5	|3|	2020-01-08 21:10:57	|10.0|	15|	None|
|6	|3	|None	|None|	None|	Restaurant Cancellation|
|7	|2|	2020-01-08 21:30:45|	25.0|	25|	None|
|8	|2|	2020-01-10 00:15:02|	23.4|	15	|None|
|9	|2	|None|	None	|None|	Customer Cancellation|
|10	|1	|2020-01-11 18:50:20|	10.0	|10	|None|

### 2. customer_orders table
````select * from customer_order````
|order_id	|customer_id|	pizza_id|	exclusions|	extras|	order_time|
|-|-|-|-|-|-|
|1	|101|	1| |		|	2020-01-01 18:05:02|
|2	|101|	1	|||		2020-01-01 19:00:52|
|3|	102|	1	|||		2020-01-02 23:51:23|
|3	|102	|2||		None|	2020-01-02 23:51:23|
|4	|103|1	|4	||	2020-01-04 13:23:46|
|4	|103|	1	|4||		2020-01-04 13:23:46|
|4	|103	|2	|4||		2020-01-04 13:23:46|
|5|	104	|1|	null|	1	|2020-01-08 21:00:29|
|6	|101	|2	|null|	null|	2020-01-08 21:03:13|
|7	|105	|2	|null	|1	|2020-01-08 21:20:29|
|8	|102	|1	|null	|null|	2020-01-09 23:54:33|
|9|	103|	1|	4	|1, 5|2020-01-10 11:22:59|
|10	|104	|1	|null	|null	|2020-01-11 18:34:49|
|10	|104|	1|	2, 6	|1, 4	|2020-01-11 18:34:49|

***Data status***
> There are blank spaces and `null` values in exclusions and extras columns.

***Cleaning process***
> Remove all blank spaces and `null` values in exclusion and extras columns 
````
create temporary table customer_orders_temp as
select
  order_id,
  customer_id,
  pizza_id, 
case
  when exclusions is null or exclusion like 'null' then null
  else exclusions
  end as exclusions,
case 
  when extras is null or extras like 'null' then null
  else extras
  end as extras,
order_time
from
  customer_orders
````
***Cleaned customer_orders_temp table***
|order_id	|customer_id|	pizza_id	|exclusions|	extras|	order_time|
|-|-|-|-|-|-|
|1	|101|	1	|None	|None|	2020-01-01 18:05:02|
|2	|101	|1|	None	|None|	2020-01-01 19:00:52|
|3	|102|	1	|None|	None	|2020-01-02 23:51:23|
|3|	102|	2	|None	|None|	2020-01-02 23:51:23|
|4	|103	|1|	4	|None	|2020-01-04 13:23:46|
|4	|103|	1	|4|	None	|2020-01-04 13:23:46|
|4	|103	|2	|4|None	|2020-01-04 13:23:46|
|5	|104	|1|	None	|1|2020-01-08 21:00:29|
|6	|101	|2	|None|	None|	2020-01-08 21:03:13|
|7	|105	|2|	None	|1	|2020-01-08 21:20:29|
|8	|102	|1|	None|	None	|2020-01-09 23:54:33|
|9|	103	|1	|4|	1, 5|	2020-01-10 11:22:59|
|10	|104|	1	|None	|None	|2020-01-11 18:34:49|
|10	|104	|1	|2, 6|	1, 4|	2020-01-11 18:34:49|

### 3. pizza_recipes table
```select * from pizza_recipes```
|pizza_id	|toppings|
|-|-|
|1	|1,2,3,4,5,6,8,10|
|2|	4,6,7,9,11,12|

> Split strings in `toppings` column into separate rows using **Pandas** because other methods are too hard. Send help 😿
````
pizza_recipes_temp = (df.set_index(['pizza_id']) 
       .apply(lambda col: col.str.split(','))
       .explode(['toppings'])
       .reset_index()
       .reindex(df.columns, axis=1))
pizza_recipes_temp
````
|	|pizza_id	|toppings|
 |-|-|-|
|0	|1|	1|
|1|	1	|2|
|2|	1	|3|
|3	|1|	4|
|4|	1	|5|
|5	|1	|6|
|6|	1	|8|
|7	|1|	10|
|8	|2|	4|
|9	|2	|6|
|10	|2	|7|
|11|	2	|9|
|12	|2|	11|
|13	|2|	12|


:octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat::octocat:
