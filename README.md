SELECT * FROM dataset_db.transactions;
CREATE TABLE sales AS
SELECT *
FROM dataset_db.transactions;
select * from sales ;
                                              /*--data cleaning-- 
                                           --step 1: to check for duplicates*/
select transaction_id ,count(*)
from sales 
group by transaction_id
having count(transaction_id)>1;
CREATE TABLE sales_clean AS
SELECT DISTINCT *
FROM sales;
DROP TABLE sales;
RENAME TABLE sales_clean TO sales;
select * from sales ;
                                    /* rename the headers name which are incorrect*/
alter table sales
rename column price to price_product;
                                          /* to check datatype*/
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'sales'
AND TABLE_SCHEMA = 'dataset_db';
SELECT DATABASE();
SHOW TABLES;
SHOW TABLES FROM dataset_db;
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_NAME = 'sales';
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'sales'
AND TABLE_SCHEMA = 'dataset_db';
DESC sales;
alter table sales
modify column purchase_date  date;
alter table sales 
modify column time_of_purchase time;
                                       /* to check null values*/
                                       /* to check null count*/
SELECT COUNT(*) AS null_count
FROM sales
WHERE transaction_id IS NULL
   OR customer_id  IS NULL
   OR customer_name IS NULL
   OR customer_age IS NULL
   OR gender IS NULL
   OR product_id IS NULL
      OR  product_name IS NULL
     OR  product_category IS NULL
     OR  quantity IS NULL
      OR price_product IS NULL
     OR  payment_mode IS NULL
      OR purchase_date IS NULL
      OR time_of_purchase IS NULL;
	                                        /* data cleaning*/
    select * from sales;
   update sales 
   set gender ="Female"
   where  gender ="F";
    
SET SQL_SAFE_UPDATES = 0;
update sales
set  gender ="Male"
where gender ="M";
UPDATE sales
SET gender = 'Female'
WHERE gender = 'female';
select distinct payment_mode from sales;
update sales
set payment_mode="Credit card"
where payment_mode="CC";
  select * from sales;
                                          /* data analysis*/
                       /* Question:1 what are the top 5 most selling product by quantity*/
select product_category ,sum(quantity) as quantity_sold 
from sales
where status="delivered"
group by  product_category
order by  quantity_sold DESC
limit 5;
/* business problem:we donts know which product are most in demand*/
/* business impact :help to priotrize stocks and boasts sales through targeted promotions*/
                                    /*Question 2: which product are most frequently cancelled*/
select product_name, count(status) as count_status
from sales 
where status="cancelled"
group by  product_name
order by  count_status DESC
limit 10;
/* busniess problem:frequent cancellation effect customer trust */
/* business impact: identifying the poor performing product to improve quality and remove from catalog*/
						        /* Question 3 :what time of the day has higher number of purchase */
SELECT time_of_purchase,
       COUNT(*) AS total_purchases
FROM sales
GROUP BY time_of_purchase
ORDER BY total_purchases DESC
limit 10;
select * from sales;
SELECT
    CASE
        WHEN HOUR(time_of_purchase) BETWEEN 0 AND 5 THEN 'Night'
        WHEN HOUR(time_of_purchase) BETWEEN 6 AND 11 THEN 'Morning'
        WHEN HOUR(time_of_purchase) BETWEEN 12 AND 17 THEN 'Afternoon'
        WHEN HOUR(time_of_purchase) BETWEEN 18 AND 23 THEN 'Evening'
    END AS time_of_day,
    COUNT(*) AS total_orders
FROM sales
GROUP BY time_of_day
ORDER BY total_orders DESC;
/* business problem solved: find peak sales time 
business impact: optimize staffing and promotions and promotion*/
                             /* Question4 : who are the top 5 spending customers */
select customer_id,customer_name,Sum(price_product*quantity) as max_price 
from sales 
where status="delivered"
group by customer_id,customer_name
order by max_price DESC
limit 5;
/* business problem solved: identify VIP customers
business impact: personalized offers , rentention and loyalty rewards*/ 
                            /* Question5 : which product category generate highest revenue*/
select product_category, sum(price_product*quantity)  as revenue
from sales 
where status="delivered"
group by product_category
order by revenue DESC
limit 5;
/* business problem solved: top performing product category
business impact: refine product strategy ,supply chain nad promotion.
allow the business to invest more in high margin or high demand categories */
                         /* Question6 : which is return and cancellation rate per product category */
SELECT product_category,
       COUNT(CASE WHEN status = 'cancelled' THEN 1 END) AS cancelled_orders
FROM sales
GROUP BY product_category
ORDER BY cancelled_orders DESC;
SELECT product_category,
       COUNT(CASE WHEN status = 'returned' THEN 1 END) AS returned_orders
FROM sales
GROUP BY product_category
ORDER BY returned_orders DESC;
/* business problem solved: moniter dissatisfaction trend per category,
business impact: reduce return ,improve product description/ expectation
helps identify and fix product or logistics issues */
                               /* Question7 : which is most prefered payment mode  */
select payment_mode, count(*) as most_prefered
from sales 
group by payment_mode
order by most_prefered DESC;
 /* business problem solved: know which option customer prefered,
business impact: streamline payment process ,priotritize popular needs*/
                              /* Question 8 : how does age group affect purchasing behaviour */
select 
      case
          when customer_age between 18 AND 25 then "18-25"
           when customer_age between 26 AND 35 then "26-35"
            when customer_age between 36 AND 50 then "36-50"
              when customer_age between 50 AND 60 then "50-60"
              else "51+"
			END as customer_age,
		SUM(price_product*quantity) as total_purchased
	from sales 
    group by case
          when customer_age between 18 AND 25 then "18-25"
           when customer_age between 26 AND 35 then "26-35"
            when customer_age between 36 AND 50 then "36-50"
              when customer_age between 50 AND 60 then "50-60"
              else "51+"
              END
    order by total_purchased DESC;
     /* business problem solved: understand customer demographic 
business impact: targated marketing and product recommendation by age group */
                                        /* Question 9:   whats monthly sales trends*/  
SELECT
    YEAR(purchase_date) AS year,
    MONTH(purchase_date) AS month,
    SUM(price_product) AS total_sales
FROM sales
WHERE status = 'delivered'
GROUP BY YEAR(purchase_date), MONTH(purchase_date)
ORDER BY YEAR(purchase_date), MONTH(purchase_date);
     /* business problem solved: sales fluctuation go unnoticed
business impact: plan inventory and marketing according to seasonal trends  */
                              /* Question 10: Are certain genders buying more specific product categories*/  
select gender,product_category,count( product_category) as total_purchase 
from sales 
group by gender,product_category
order by total_purchase DESC
     /* business problem solved: gender based product preference 
business impact: plan inventory and marketing according to seasonal trends  */
/*This project demonstrates how SQL-based data cleaning and analysis can transform raw transactions
into actionable insights. The findings support better decision-making in sales, marketing, and operations,
ultimately driving customer satisfaction and business growth*/# SQL-SALES-PROJECT-
The goal of this project was to clean, transform, and analyze sales transaction data from the dataset_db.transactions table to uncover meaningful business insights. By applying SQL queries, we ensured data quality and performed exploratory analysis to answer key business questions.
