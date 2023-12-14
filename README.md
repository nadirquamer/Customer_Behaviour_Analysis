# Customer Behaviour Analysis

### Project Overview

This project and the data used were part of a case study which can be found [here](https://8weeksqlchallenge.com/case-study-1/). It focuses on examining patterns, trends, and factors influencing customer spending to gain insights into their preferences, purchasing habits, and potential areas for improvement in menu offerings or marketing strategies in a dining establishment.

### Background

Danny seriously loves Japanese food so at the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry, and ramen. Danny’s Diner needs your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from its few months of operation but has no idea how to use its data to help it run the business.

### Problem Statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent, and which menu items are their favorite. Having this deeper connection with his customers will help him deliver a better and more personalized experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally, he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally, he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

### Entity Relationship Diagram

![diagram](https://github.com/nadirquamer/Customer_Behaviour_Analysis/assets/46354703/0f9ead17-6ccd-4c2b-8b2e-a422a051e936)

### Skills Applied

- Window Function
- CTEs
- Aggregations
- Joins
- Write scripts to generate basic reports that can be run every period

### Questions Explored

1. What is the total amount each customer spent at the restaurant?
2. How many days has each customer visited the restaurant?
3. What was the first item from the menu purchased by each customer?
4. What is the most purchased item on the menu and how many times was it purchased by all customers?
5. Which item was the most popular for each customer?
6. Which item was purchased first by the customer after they became a member?
7. Which item was purchased just before the customer became a member?
8. What are the total items and amount spent for each member before they became a member?
9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and 
    have at the end of January?

### Some interesting queries

Q5. Which item was the most popular for each customer?

```sql
WITH customer_popularity AS (
    SELECT s.customer_id, m.product_name, COUNT(*) AS purchase_count,
        DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(*) DESC) AS rank
    FROM dbo.sales s
    INNER JOIN dbo.menu m ON s.product_id = m.product_id
    GROUP BY s.customer_id, m.product_name
)
SELECT customer_id, product_name, purchase_count
FROM customer_popularity
WHERE rank = 1;
```

Q10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

```sql
SELECT s.customer_id, SUM(
    CASE 
        WHEN s.order_date BETWEEN mb.join_date AND DATEADD(day, 7, mb.join_date) THEN m.price*20
        WHEN m.product_name = 'sushi' THEN m.price*20 
        ELSE m.price*10 
    END) AS total_points
FROM dbo.sales s
JOIN dbo.menu m ON s.product_id = m.product_id
LEFT JOIN dbo.members mb ON s.customer_id = mb.customer_id
WHERE s.customer_id IN ('A', 'B') AND s.order_date <= '2021-01-31'
--WHERE s.customer_id = mb.customer_id AND s.order_date <= '2021-01-31'
GROUP BY s.customer_id;
```

### Insights

- Customer B is the most frequent visitor with 6 visits in Jan 2021.
- Danny’s Diner’s most popular item is ramen, followed by curry and sushi.
- Customer A loves ramen, Customer C loves only ramen whereas Customer B seems to enjoy sushi, curry, and ramen equally.
- The last items ordered by Customers A and B before they became members were sushi and curry. Does it mean both of these items are the deciding factor?




