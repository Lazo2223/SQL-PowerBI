# SQL-PowerBI
Making a PowerBI dashboard and excersising SQL. Dataset is being downloaded from Kaggle, and will be attached here also.
Filtering for Body style and month. Year, engine and transmission.
Calculating total Revenue, avg Car price, the gender who buys a car based on make, model, color and body style.

![PowerBI_car_sales_dashboard](https://github.com/user-attachments/assets/53e5ce66-cd8c-44db-8df0-a5e2c2168a1f)



#SQL queries on this dataset:
CSV file was imported in PostgresSQL.


1. Top-Selling Cars by Month
Find the best-selling car models each month.
Using RANK() to get the top 3 cars per month.
```
WITH filter_months AS (SELECT company, model, EXTRACT('month' FROM date) as get_month
FROM car_sales),

month_sales AS (SELECT model, COUNT(model) AS num_sold,get_month
				FROM filter_months
				GROUP BY model, get_month
				ORDER BY get_month ASC),

ranking as (SELECT 
		    model, 
		    num_sold, 
		    get_month,
    DENSE_RANK() OVER (PARTITION BY get_month ORDER BY num_sold DESC) AS rank
FROM month_sales
ORDER BY get_month ASC, rank ASC)

SELECT * FROM ranking
WHERE rank in (1,2,3)
```

We see that the top 3 sold cars each month. What other we see with the result - same number of sold cars are presented with tie position. There could be more than 3 models returned. This is because of DENSE_RANK functions, further filtering will be needed.
<img width="1437" alt="Screenshot 2025-03-24 at 14 48 06" src="https://github.com/user-attachments/assets/97d47576-c8d0-4685-9493-38e690f63271" />



2. Customer Segmentation
Categorize customers based on their annual income into three groups (low, medium, high).
Useing NTILE(3) to distribute them.
```
WITH salary AS (SELECT gender, annual_income,
NTILE(3) OVER(ORDER BY annual_income ASC) as split_salary
FROM car_sales)

SELECT ROUND(AVG(annual_income),2), split_salary
FROM salary
GROUP BY split_salary
ORDER BY split_salary ASC
```
Here we see salaries in splited in 3 categories. Where the highest earners are earning 1.596.867,99



<img width="580" alt="Screenshot 2025-03-24 at 14 52 58" src="https://github.com/user-attachments/assets/9e49dbac-3b06-4335-b1d5-b9ffcae61b85" />


3.Revenue Growth Analysis
Calculate total revenue per month.

Getting Month to month revanue:
```
WITH filtering AS (SELECT SUM(price) AS total_revanue, get_month FROM(
		SELECT price, EXTRACT('month'FROM date) AS get_month
		FROM
		car_sales)
		GROUP BY get_month
		ORDER BY get_month ASC),

lagging AS 	(SELECT total_revanue, get_month,
		LAG(total_revanue,1) OVER() AS prev_month_value
		FROM filtering)

SELECT total_revanue, prev_month_value, get_month, total_revanue - prev_month_value AS difference
FROM lagging
```
<img width="1435" alt="Screenshot 2025-03-24 at 14 58 10" src="https://github.com/user-attachments/assets/71a2c33e-2e58-4181-b58c-73e4d5c061d1" />

<ins>T**he goal of this analysis**</ins>
The best selling car of this dataset is the VW Jetta, with 76 units sold on October.
to-do

<ins>**Key insights**</ins>

to-do

<ins>**Business use**</ins>

Car dealerships and manufacturers rely on data-driven insights to maximize sales, optimize inventory, and improve customer targeting. By analyzing car sales data, businesses can:
	•	Identify top-selling models and adjust inventory accordingly.
	•	Detect seasonal trends to plan promotions and discounts.
	•	Segment customers based on preferences, demographics, and buying behavior.
	•	Predict future demand using historical sales patterns and external factors like fuel prices or economic trends.

This analysis can help businesses increase revenue, reduce excess inventory costs, and enhance customer satisfaction through data-backed decision-making.

