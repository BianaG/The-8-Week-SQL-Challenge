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

````
