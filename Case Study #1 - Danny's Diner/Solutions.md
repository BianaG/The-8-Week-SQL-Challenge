
# Case Study #1 - Danny's Diner 🍜



**1. What is the total amount each customer spent at the restaurant?**
````sql
SELECT
customer_id,
SUM(price) AS amount_spent

FROM dannys_diner.sales JOIN dannys_diner.menu
ON sales.product_id = menu.product_id

GROUP BY customer_id
ORDER BY amount_spent DESC;
````

**Answer:**
| customer_id | amount_spent |
| ----------- | ------------ |
| A           | 76           |
| B           | 74           |
| C           | 36           |

***

**2. How many days has each customer visited the restaurant?**
````sql
SELECT customer_id,
       COUNT(DISTINCT(order_date)) AS num_visit_days

FROM dannys_diner.sales
GROUP BY customer_id
ORDER BY num_visit_days DESC;
````
    
**Answer:**

| customer_id | num_visit_days |
| ----------- | -------------- |
| B           | 6              |
| A           | 4              |
| C           | 2              |

***

**3. What was the first item from the menu purchased by each customer?**
````sql
SELECT customer_id,
           product_name AS first_purchased_item
           
    FROM (SELECT DISTINCT customer_id,
                           product_id
          FROM dannys_diner.sales
          WHERE order_date IN (SELECT MIN(order_date) AS first_date
                               FROM dannys_diner.sales
                               GROUP BY customer_id)) AS first_prod_id
                               
    LEFT JOIN dannys_diner.menu AS me
    ON first_prod_id.product_id = me.product_id;

````


**Answer:**

| customer_id | first_purchased_item |
| ----------- | -------------------- |
| A           | sushi                |
| A           | curry                |
| B           | curry                |
| C           | ramen                |

***

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
````sql


````
