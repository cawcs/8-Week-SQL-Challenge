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
### **1. How many pizzas were ordered?
````
select
  count(order_id) as pizza_ordered
from
  customer_orders
````
