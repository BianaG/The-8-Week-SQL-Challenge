
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
SELECT product_name,
           num_purchases
           
    FROM (SELECT product_id,
           COUNT(product_id) AS num_purchases
    
    FROM dannys_diner.sales
    GROUP BY product_id
    ORDER BY num_purchases DESC) AS prod_num_purch
    
    LEFT JOIN dannys_diner.menu AS me
    ON prod_num_purch.product_id = me.product_id
    
    ORDER BY num_purchases DESC
    LIMIT 1;
````

**Answer:**

| product_name | num_purchases |
| ------------ | ------------- |
| ramen        | 8             |

***

**5. Which item was the most popular for each customer?**
````sql
SELECT r.customer_id,
       me.product_name,
       r.num_prod
           
    FROM(SELECT
      	customer_id,
        product_id,
        COUNT(product_id) AS num_prod,
        DENSE_RANK()
        OVER(PARTITION BY customer_id ORDER BY COUNT(product_id) DESC) AS rank_prod
        FROM dannys_diner.sales
        GROUP BY customer_id, product_id) AS r
        
    LEFT JOIN dannys_diner.menu AS me
    ON r.product_id = me.product_id
    
    WHERE r.rank_prod = 1
    ORDER BY customer_id;
````


**Answer:**

| customer_id | product_name | num_prod |
| ----------- | ------------ | -------- |
| A           | ramen        | 3        |
| B           | sushi        | 2        |
| B           | curry        | 2        |
| B           | ramen        | 2        |
| C           | ramen        | 3        |

***

**6. Which item was purchased first by the customer after they became a member?**
````sql

    WITH cus_join_date AS
    (
      SELECT s.customer_id,
             mem.join_date,
             s.product_id,
             DENSE_RANK() OVER(
               PARTITION BY s.customer_id 
               ORDER BY s.order_date) AS rank
      FROM dannys_diner.sales AS s
      LEFT JOIN dannys_diner.members AS mem
      ON s.customer_id = mem.customer_id
      WHERE s.order_date >= mem.join_date
    )
      
      SELECT cjd.customer_id,
             cjd.join_date,
             me.product_name
      FROM cus_join_date AS cjd
      LEFT JOIN dannys_diner.menu AS me
      ON cjd.product_id = me.product_id
      WHERE rank = 1
      ORDER BY cjd.customer_id;
 ````

**Answer:**

| customer_id | join_date                | product_name |
| ----------- | ------------------------ | ------------ |
| A           | 2021-01-07T00:00:00.000Z | curry        |
| B           | 2021-01-09T00:00:00.000Z | sushi        |

---

**7. Which item was purchased just before the customer became a member?**
````sql

    WITH cus_join_date AS
    (
      SELECT s.customer_id,
             s.order_date,
             mem.join_date,
             s.product_id,
             DENSE_RANK() OVER(
               PARTITION BY s.customer_id 
               ORDER BY s.order_date DESC) AS rank
      FROM dannys_diner.sales AS s
      LEFT JOIN dannys_diner.members AS mem
      ON s.customer_id = mem.customer_id
      WHERE s.order_date < mem.join_date
    )
      
      SELECT cjd.customer_id,
             cjd.order_date,
             cjd.join_date,
             me.product_name
      FROM cus_join_date AS cjd
      LEFT JOIN dannys_diner.menu AS me
      ON cjd.product_id = me.product_id
      WHERE cjd.rank = 1
      ORDER BY cjd.customer_id, cjd.order_date;
 ````
**Answer:**

| customer_id | order_date               | join_date                | product_name |
| ----------- | ------------------------ | ------------------------ | ------------ |
| A           | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | sushi        |
| A           | 2021-01-01T00:00:00.000Z | 2021-01-07T00:00:00.000Z | curry        |
| B           | 2021-01-04T00:00:00.000Z | 2021-01-09T00:00:00.000Z | sushi        |

---


