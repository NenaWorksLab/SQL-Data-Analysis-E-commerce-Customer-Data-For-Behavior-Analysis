# SQL Data Analysis:  E-commerce Customer Data For Behavior Analysis



![SQLite](https://img.shields.io/badge/sqlite-%2307405e.svg?style=for-the-badge&logo=sqlite&logoColor=white)
![Kaggle](https://img.shields.io/badge/Kaggle-035a7d?style=for-the-badge&logo=kaggle&logoColor=white)


## Project Overview
This project focuses on analyzing customer behavior from a large-scale e-commerce dataset. The goal is to gain valuable insights into customer purchasing habits, identify key customer segments, and measure important business metrics such as customer retention and purchase frequency.
The analysis was performed using advanced SQL techniques to handle and query a large volume of data efficiently.

***

## Problem Statement & Key Questions
The main objective of this analysis is to answer key business questions, including:
**1. Customer Value Analysis:**
Who are the top-spending customers?
**2. Customer Behavior & Lifecycle Analysis:**
What is the overall customer retention rate for the business?
What is the average time between purchases for a customer?
**3. Customer Segmentation:**
What are the most valuable customer segments based on their spending and purchase frequency?

***


## Dataset
The dataset used is a [ E-commerce Customer Data For Behavior Analysis](https://www.kaggle.com/datasets/shriyashjagtap/e-commerce-customer-for-behavior-analysis) that was provided in two separate files. The dataset contains key customer information, including Customer ID, Purchase Date, and Total Purchase Amount, which are essential for understanding customer behavior. While the dataset is synthetic, it provides a realistic foundation for practicing data wrangling and advanced SQL analysis techniques to derive meaningful insights, such as customer retention and purchase frequency.

| Column | Data Type | Description | 
| ------ | ------ |------ |
|Customer ID | `INT` |A unique identifier for each customer |
| Customer Name | `VARCHAR` |The name of the customer (generated) |
| Customer Age | `INT` | The age of the customer (generated) |
| Gender | `VARCHAR` | The gender of the customer (generated) |
| Purchase Date | `DATE` | The date of each purchase |
| Product Category | `VARCHAR` | The category or type of purchased product |
| Product Price | `DECIMAL` |The price of the product |
| Quantity | `INT` | 	The quantity of the product purchased|
| Total Purchase Amount |`DECIMAL` | The total amount spent in each transaction |
| Payment Method | `VARCHAR` | The payment method used by the customer |
| Returns | `TINYINT`  | A binary column indicating a return (0 for no, 1 for yes) |
| Churn | `TINYINT` | A binary column indicating customer churn (0 for retained, 1 for churned) |

***

## Tools Used
- **Database:** DB Browser for SQLite
- **Client:** DB Browser for SQLite
- **Language:** Structured Query Language (SQL)

***

## Analysis & Key Findings
###  1. Customer Value Analysis: 
#### Who are the top-spending customers?
```sh
SELECT
    "Customer ID",
    "Customer Name",
    SUM("Total Purchase Amount") AS total_spending,
    RANK() OVER (ORDER BY SUM("Total Purchase Amount") DESC) AS spending_rank
FROM
    full_ecommerce_data
GROUP BY
    "Customer ID",
    "Customer Name"
ORDER BY
    spending_rank;
```
##### Analysis & Findings

![the top-spending customers](https://github.com/NenaWorksLab/SQL-Data-Analysis-E-commerce-Customer-Data-For-Behavior-Analysis/blob/main/Result/1.png?raw=true)

>The top customer was Jesse Gray, with total spending of $55,339. The top five customers exhibited very high and similar spending levels, with totals ranging from $50,179 to $55,339.


###  2. Customer Behavior & Lifecycle Analysis:
#### What is the overall customer retention rate for the business?

```sh
WITH first_purchase AS (
    SELECT
        "Customer ID",
        MIN("Purchase Date") AS first_date
    FROM full_ecommerce_data
    GROUP BY "Customer ID"
)
SELECT
    COUNT(T1."Customer ID") AS total_first_time_customers,
    COUNT(T2."Customer ID") AS returning_customers
FROM first_purchase AS T1
LEFT JOIN full_ecommerce_data AS T2
  ON T1."Customer ID" = T2."Customer ID"
 AND (JULIANDAY(T2."Purchase Date") - JULIANDAY(T1.first_date)) > 0;
```

##### Analysis & Findings

![Which products are the most popular, and from which categories and brands](https://github.com/NenaWorksLab/SQL-Data-Analysis-E-commerce-Customer-Data-For-Behavior-Analysis/blob/main/Result/2.png?raw=true)

> Of all customers who made their first purchase, only 20 did not return. This finding indicates that your customer retention rate is very high and nearly perfect.

#### What is the average time between purchases for a customer?

```sh
 WITH customer_purchase_history AS (
    SELECT
        "Customer ID",
        "Purchase Date",
        LAG("Purchase Date", 1) OVER (
            PARTITION BY "Customer ID"
            ORDER BY "Purchase Date"
        ) AS previous_purchase_date
    FROM
        full_ecommerce_data
)
SELECT
    "Customer ID",
    AVG(JULIANDAY("Purchase Date") - JULIANDAY(previous_purchase_date)) AS avg_days_between_purchases
FROM
    customer_purchase_history
WHERE
    previous_purchase_date IS NOT NULL
GROUP BY
    "Customer ID"
ORDER BY
    avg_days_between_purchases;
```

##### Analysis & Findings

![average time between purchases for a customer](https://github.com/NenaWorksLab/SQL-Data-Analysis-E-commerce-Customer-Data-For-Behavior-Analysis/blob/main/Result/3.png?raw=true)


> There are 166 products that are low on inventory (less than 5 units) in some stores. The company must immediately replenish these stocks to avoid lost sales.

###  3. Customer Segmentation:
#### What are the most valuable customer segments based on their spending and purchase frequency?
```sh
SELECT 
	c.first_name,
    c.last_name,
    SUM(oi.quantity * oi.list_price * (1 - oi.discount)) AS total_sales
FROM sales.customers AS c 
JOIN sales.orders AS o 
	ON c.customer_id = o.customer_id
JOIN sales.order_items AS oi 
	ON o.order_id = oi.order_id
GROUP BY 
	 c.customer_id,
    c.first_name,
    c.last_name
ORDER BY 
	total_sales DESC
LIMIT 5; 
```

##### Analysis & Findings

![ Who are the top 5 most valuable customers based on spending](https://github.com/NenaWorksLab/SQL-Data-Analysis-Bike-Store-Relational-Database/blob/main/Result/3.1.%20Who%20are%20the%20top%205%20most%20valuable%20customers%20based%20on%20spending.png?raw=true)

> The top five customers, like Sharyn Hopkins, have contributed $34,807 of total revenue. Loyalty programs can be focused on this customer segment.

#### Customer Regions
```sh
SELECT
	state,
    city,
    COUNT(*) AS total_customer
FROM sales.customers
GROUP BY 
	state,
    city
ORDER BY total_customer DESC;
```
##### Analysis & Findings

![ From which regions do most customers come](https://github.com/NenaWorksLab/SQL-Data-Analysis-Bike-Store-Relational-Database/blob/main/Result/3.%202.%20From%20which%20regions%20do%20most%20customers%20come.png?raw=true)

> the majority of customers come from Mount Vernon, accounting for 20 consumers. This indicates that the store has a strong customer base in that specific city.

## Conclusion
###  1. Customer Value Analysis

| What Was Found(WHAT)| Implications / Why (SO WHAT) | Recommendations (NOW WHAT)|
| ------ | ------ | ------ |
| The top customer was Jesse Gray, with total spending of $55,339. The top five customers exhibited very high and similar spending levels, with totals ranging from $50,179 to $55,339. | The significant revenue contribution of these small numbers of customers demonstrates that they are the foundation of a business’s revenue. Maintaining their satisfaction and loyalty is crucial to long-term success. | Set up a loyalty program: Create a special program or offer personalized discounts to encourage repeat orders and increase customer retention. |

###  2. Customer Behavior & Lifecycle Analysis
| What Was Found(WHAT)| Implications / Why (SO WHAT) | Recommendations (NOW WHAT)|
| ------ | ------ | ------ |
| Only 20 of the total customers did not return to make a transaction after their first purchase. | This near-perfect retention rate indicates strong product-market fit. | Identify and analyze the behavior of those 20 non-returning customers to find out why they didn't return (for example, through surveys or behavioral data analysis). Use these insights to further improve the customer experience to maintain high retention rates. |
| An average lag time of 5.57 days indicates highly loyal customers, while a lag time of 27.17 days identifies less frequent buyers. | This variety shows that your business caters to a wide range of shoppers, from highly loyal and frequent shoppers to more casual shoppers. | Create a tailored marketing strategy for each segment. For customers with a short lag time, focus on loyalty programs and exclusive offers. For customers with a longer lag time, use campaigns that focus more on reminders or promotions that encourage repeat purchases more quickly. |

###  3. Customer Segmentation
| What Was Found(WHAT)| Implications / Why (SO WHAT) | Recommendations (NOW WHAT)|
| ------ | ------ | ------ |
| The Loyal & High-Value segment dominates with 48,561 customers.| This highly loyal customer base is the main engine driving your business's revenue.| Create an exclusive loyalty program, offer personalized discounts, or provide early access to new products for this segment. The goal is to strengthen the relationship and increase their lifetime value. |
| The New/Infrequent Buyer segment is the smallest, with only 20 customers. | This very small number indicates that most of your current strategies are already very effective in converting new buyers into loyal customers. | Conduct in-depth analysis on these 20 customers to understand why they are not returning.|

## Contribution
This project was developed by **Nenna Khoirunnisa** as a self-initiated learning and portfolio endeavor.

* **GitHub:** [github.com/NenaWorksLab](https://github.com/NenaWorksLab)
* **LinkedIn:** [linkedin.com/in/nenna-khoirunnisa](https://www.linkedin.com/in/nenna-khoirunnisa-4852101b4/)


