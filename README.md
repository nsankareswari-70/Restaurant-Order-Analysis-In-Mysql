### Restaurant-Order-Analysis-In-Mysql

Project Description: Restaurant Order Data Analysis

This project focuses on analyzing transactional restaurant data to uncover insights about menu performance and customer preferences. Using the order_details and menu_items tables, the goal is to understand which dishes are most popular, which items underperform, and what patterns emerge from top-spending or frequent customers.

``` sql

DROP SCHEMA IF EXISTS restaurant_db;
CREATE SCHEMA restaurant_db;
USE restaurant_db;

--
-- Table structure for table `order_details`
--
CREATE TABLE order_details (
  order_details_id SMALLINT NOT NULL,
  order_id SMALLINT NOT NULL,
  order_date DATE,
  order_time TIME,
  item_id SMALLINT,
  PRIMARY KEY (order_details_id)
);

--
-- Table structure for table `menu_items`
--

CREATE TABLE menu_items (
  menu_item_id SMALLINT NOT NULL,
  item_name VARCHAR(45),
  category VARCHAR(45),
  price DECIMAL(5,2),
  PRIMARY KEY (menu_item_id)
);

```

```sql
-- Objectives:
-- 1.View the menu_items table
-- 2. Find the number of items on the menu.
-- 3. What are the least and most expensive items on the menu?
-- 4. How many Italian dishes are on the menu?
-- 5. What are the least and most expensive Italian dishes on the menu?
-- 6. How many dishes are in each category?
-- 7. What is the average dish price within each category?

-- Results
-- 1.View the menu_items table
select * from menu_items;

-- 2. Find the number of items on the menu.
select count(distinct(menu_item_id)) as Number_of_Menu_Items from menu_items;
```
<img src="https://github.com/nsankareswari-70/Restaurant-Order-Analysis-In-Mysql/blob/076c27c6bab8a59dda555bfd1be27b695d3cd5d9/rest1.png">

```sql
-- 3. What are the least and most expensive items on the menu?
-- Least expensive item on the menu
select * from menu_items order by price limit 1;
-- Most expensive item on the menu
select * from menu_items order by price desc limit 1;

-- 4. How many Italian dishes are on the menu?
select count(category) as Total_Italian_Dishes from menu_items 
where category ='Italian';

-- 5. What are the least and most expensive Italian dishes on the menu?
select * from menu_items where category='Italian' order by price desc;

-- 6. How many dishes are in each category?
select category,count(category) as Total_dishes from menu_items 
group by category order by Total_dishes desc;

-- 7. What is the average dish price within each category?
select category,round(avg(price),2) as Avg_dish_price from menu_items
group by category order by Avg_dish_price desc;
```

```sql
-- Queries for Order_details table
-- 1. View the order_details table
select * from order_details;

-- 2. What is the date range of the table?
select min(order_date) as Startdate,max(order_date) as Enddate from order_details;

-- 3. How many orders were made within this date range?
select count(distinct(order_id)) as Total_orders from order_details;

-- 4. How many items were ordered within this date range?
select count(*) as Total_items_ordered from order_details;

-- 5. which orders had the most number of items?
select order_id,count(*) as Number_of_items from order_details
group by order_id order by Number_of_items desc;
-- 6. How many orders had more than 12 items
select count(*) from
(
select order_id,count(item_id) as Number_of_items from order_details
group by order_id having Number_of_items > 12 order by
Number_of_items desc) as tw;

select * from menu_items;
select * from order_details;

-- 1. Combine the menu_items and order_details tables into a single table
select od.order_details_id,od.order_id,od.order_date,od.order_time,od.item_id,
mi.menu_item_id,mi.item_name,mi.category,mi.price from order_details od join
menu_items mi on
od.item_id = mi.menu_item_id;

-- 2. what were the least and most ordered items? what categories were they in?
select item_id,item_name,category,count(item_id) as least_ordered_item from 
order_details od join menu_items mi on
od.item_id = mi.menu_item_id
group by item_id order by least_ordered_item limit 1;

select item_id,item_name,category,count(item_id) as most_ordered_item from 
order_details od join menu_items mi on
od.item_id = mi.menu_item_id
group by item_id order by most_ordered_item desc limit 1;

-- 3. what were the top 5 orders that spent the most money?
select order_id,sum(price) as Total_spent from order_details od
join menu_items mi on
od.item_id = mi.menu_item_id
group by order_id order by Total_spent desc limit 5;

-- 4. View the details of the Highest spent order.
select order_id,sum(price) as Total_spent from order_details od
join menu_items mi on
od.item_id = mi.menu_item_id
group by order_id order by Total_spent desc limit 1;

-- From this to get the details of order_id 440
select order_id,item_name,category,price from order_details od join
menu_items mi on 
od.item_id = mi.menu_item_id where order_id=440;

select category,count(category) as itemspercategory from order_details od join
menu_items mi on 
od.item_id = mi.menu_item_id where order_id=440 
group by category;

-- 5.View the details of the top 5 highest spend orders. What insights can you gather from the result.
select order_id,sum(price) as Total_spent from order_details od
join menu_items mi on
od.item_id = mi.menu_item_id
group by order_id order by Total_spent desc limit 5;

-- To view the details of category and its count in the top 5 orders
select category,count(category) as itemspercategory from order_details od join
menu_items mi on 
od.item_id = mi.menu_item_id where order_id in (440,2075,1957,330,2675)
group by category;

-- To view the categories in each order_id
select order_id,category,count(category) as itemspercategory from order_details od join
menu_items mi on 
od.item_id = mi.menu_item_id where order_id in (440,2075,1957,330,2675)
group by category,order_id;
```



