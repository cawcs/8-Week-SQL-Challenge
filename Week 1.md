## ✨ Conceptual Background ✨
   <div align="justify"> Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen. Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business. </div>
 
  <div align="justify"> Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalized experience for his loyal customers.
  
  He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.
  
  Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions! </div>

  Danny has shared with you 3 key datasets for this case study: \
_sales_ \
_menu_ \
_members_ 
## Data schema
![Danny Diner](https://github.com/cawcs/8-Week-SQL-Challenge/assets/147532517/b3b1ec3d-ba29-47c9-ac24-8798ef13f117)

## ❓Questions and Solutions ✔️
### **1. What is the total amount each customer spent at the restaurant?** :octocat:
````sql
select 
  customer_id, 
  count(distinct order_date) as total_spending
from
  sales 
group by
  customer_id
````
***Answer***
| customer_id | total_spending |
| ----------- | -------------- |
| A           | 76             |
| B           | 74             |
| C           | 36             |

### **2. How many days has each customer visited the restaurant?** 🏃‍♀️
````sql
select
  customer_id,
  count(distinct order_date) as days_visited
from
  sales 
group by
  customer_id
````
***Answer***
|customer_id	|days_visited|
|-------------|------------|
|A	          |4           |
|B	          |6           |
|C	          |2           |

### **3. What was the first item from the menu purchased by each customer? 🥘🍔🍕
````
with CTE as (
    select
      customer_id,
      order_date, product_name,
    dense_rank() over(partition by customer_id order by order_date) as first_item_purchased,
    row_number() over (partition by customer_id order by order_date asc) as rn
from
  menu as m 
join
  sales as s
on
  m.product_id = s.product_id
group by
  s.customer_id,
  s.order_date,
  m.product_name
)
select
  customer_id,
  product_name
from
  CTE 
where
  first_item_purchased = 1
````

***Answer***
|customer_id|	product_name|
|-|-|
|A	|sushi|
|A	|curry|
|B	|curry|
|C	|ramen|

### **4. What is the most purchased item on the menu and how many times was it purchased by all customers? 📈⬆️
````
select
  product_name,
  count(order_date) as most_purchased
from
  menu as m 
join
  sales as s
on
  m.product_id=s.product_id
group by
  product_name
order by
  count(order_date) desc
limit 1
````
***Answer***
|product_name	|most_purchased|
|-|-|
|ramen|	8|

### **5. Which item was the most popular for each customer? ❤️‍🔥 🐈‍⬛ 🐈 🐈‍⬛ 🐈 ❤️‍🔥
````
with CTE as(
  select 
    product_name,
    customer_id, 
  count(order_date) as orders,
  dense_rank() over (partition by customer_id order by count(order_date) desc) as most_popular,
  row_number() over (partition by customer_id order by count(order_date) desc) as rn
from
  sales as s
join
  menu as m
on
  s.product_id = m.product_id
group by
  product_name,
  customer_id
)
select
  customer_id,
  product_name
from CTE
where most_popular = 1
````
***Answer***
|customer_id|	product_name|
|-|-|
|A	|ramen|
|B|	curry|
|B|	sushi|
|B	|ramen|
|C|	ramen|

### **6. Which item was purchased first by the customer after they became a member? 🌓
````
with CTE as(
select
  s.customer_id,
  order_date,
  product_name,
  join_date,
rank() over(partition by s.customer_id order by order_date) as first_purchased,
row_number() over(partition by s.customer_id order by order_date) as rn
from
  sales as s
join
  members as mb 
on
  mb.customer_id=s.customer_id
join
  menu as m 
on
  m.product_id=s.product_id
where
  order_date >= join_date
)
select
  customer_id,
  product_name
from CTE
where first_purchased=1
````
***Answer***
|customer_id	|product_name|
|-|-|
|A	|curry|
|B	|sushi|

### **7. Which item was purchased just before the customer became a member? :octocat:
````
with CTE as(
  select
    s.customer_id,
    order_date,
    product_name,
    join_date,
rank() over(partition by s.customer_id order by order_date desc) as b4_mbs,
row_number() over(partition by s.customer_id order by order_date) as rn
from
  sales as s
join
  members as mb 
on
  s.customer_id=mb.customer_id
join
  menu as m
on
  s.product_id=m.product_id
where
  order_date < join_date
order by
  order_date asc
)
select
  customer_id,
  product_name
from
  CTE
where
  b4_mbs = 1
````
***Answer***
|customer_id|	product_name|
|-|-|
|A	|sushi|
|A	|curry|
|B	|sushi|

### **8. What is the total items and amount spent for each member before they became a member? 🐱😼😽
````
select
  s.customer_id,
  count(product_name) as total_item,
  sum(price) as amount_spent
from
  sales as s 
join
  menu as m 
on
  s.product_id = m.product_id
join
  members as mb
on
  s.customer_id =mb.customer_id
where
  order_date < join_date
group by
  s.customer_id
````
***Answer***
|customer_id	|total_item|	amount_spent|
|-|-|-|
|B	|3	|40|
|A	|2	|25|

### **9.If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have? 🔯
````
select
  s.customer_id,
  sum(
    case
      when product_name = 'sushi' then price*10*2
      else price*10
    end) as points
from
  sales as s
join
  menu as m
on
  s.product_id = m.product_id
group by
  s.customer_id
````
***Answer***
|customer_id|	points|
|-|-|
|A|	860|
|B	|940|
|C|	360|

### **10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January? 
````
select
  s.customer_id,
sum(
  case
    when (timestampdiff(day, mb.join_date, s.order_date) between 0 and 7) or (m.product_ID = 1) then m.price * 20
    else m.price * 10
  end) as Points
from
  members as mb
join
  sales as s
on
  s.customer_id = mb.customer_id
join
  menu as m on m.product_id = s.product_id
where
  s.order_date >= mb.join_date and s.order_date <= cast('2021-01-31' as date)
group by
  s.customer_id
````
***Answer***
|customer_id|	Points|
|-|-|
|B	|440|
|A	|1020|

***🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈🐈‍⬛🐈***

