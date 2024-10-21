# Hi there 👋 I'm a total data junkie who loves diving into business and market intel to uncover insights hidden within the numbers!

## Excel ⬇️
### Project 6: Sales Forecast based on Historical Figures 
<details>
<summary>View Project</summary>



</details>
</br>

## SQL ⬇️

### Project 5: Global E-commerce Business Analysis
<details>
<summary>View Project</summary>
	
## Project Overview

_A Tableau interactive dashboard can be checked [here](https://public.tableau.com/app/profile/yining.zeng/viz/OnlineRetailShop_17281331942810/Dashboard1)._

A global e-commerce company, operating an online retail shop, offers a wide range of product categories across Asia, Europe, and North America. This project provides insights and recommendations in the following key areas:
 
* **Sales Trends Analysis**: Evaluates KPIs such as revenue, order volume, and average order value (AOV).

* **Inventory Management Analysis:** Analyzes product lines to understand customer purchase preferences across different regions and time periods.

* **Seasonality and Regional Comparisons:** Examines revenue performance over the past six months, highlighting seasonal trends and regional fluctuations.

* **Product Share Analysis:** Reviews historical sales patterns, focusing on the top three and bottom three products in each category based on total revenue.

## Data Structure

The dataset contains 9 fields. Before beginning the analysis, data cleaning and standardization are necessary. For example, the original value of the 'transaction_date' field is in 'text' format, which needs to be converted to a 'date' type for accurate analysis.

<img width="292" alt="Screenshot 2024-10-05 at 20 39 18" src="https://github.com/user-attachments/assets/7f7f3c1f-ca0b-4b92-8cab-d121560f8255">
</br>

## Insight Deep Dive

* Overall Overview: Key metrics such as Total Revenue, Order Volume, and Average Order Value (AOV).

1. In the past six months, we generated a total of $66K in revenue from 181 products sold, with an average order value of approximately $247.

<img width="821" alt="Screenshot 2024-10-05 at 21 33 30" src="https://github.com/user-attachments/assets/288358cb-110b-493d-b700-c5cdb5d95a70">

2. In different regions, we offer two product categories per region. Over the past six months, January, March, and May saw the highest sales volumes, with North America leading in product sales, particularly in the Electronics category. In contrast, February and June recorded the lowest sales in both Europe and North America. Considering seasonality, Q1 exhibited more dynamic fluctuations in sales compared to Q2, which showed a more stable order volume. This insight will be valuable for optimizing future inventory management.

<img width="780" alt="Screenshot 2024-10-05 at 21 43 39" src="https://github.com/user-attachments/assets/9288cb7c-d89f-45c9-82f7-9d6faea2129b">

* Revenue Distribution: Analyzing total revenue by month and region, identifying upward and downward trends.

  The total revenue indicates a declining performance, which should be a cause for concern. Considering that the prices for the 'Electronics' and 'Home Appliances' categories are higher compared to other categories, the reduced order volumes in February and June help explain the decline. Overall, the end of the first half year saw a significant drop in sales across all regions.

<img width="1275" alt="Screenshot 2024-10-05 at 21 57 16" src="https://github.com/user-attachments/assets/4159e681-8d65-4d0c-b04f-38f811b574fb">

* Product Market Share by Revenue: Identifying the Top 3 and Bottom 3 products in each category based on revenue contribution.

  To assess our marketing and procurement strategy, I analyzed the top 3 and bottom 3 products based on revenue. By considering both product price and units sold, we can identify which products contribute the highest and lowest revenue share within each category.

<img width="1269" alt="Screenshot 2024-10-05 at 22 16 17" src="https://github.com/user-attachments/assets/c2a7c991-ad98-47f5-85fa-4332dc3cab50">


## Recommendation and Communication

* ### Optimize Revenue Growth

1. **Identify Decline Causes:** Investigate the factors contributing to declining revenue, especially in February and June, and take corrective actions.
 
2. **Focus on High-Value Categories:** Boost the performance of high-value categories like 'Electronics' and 'Home Appliances' by optimizing pricing, promotions, and customer targeting.

3. **Boost Sales in Low-Volume Months:** Plan targeted marketing campaigns in February and June to drive demand and smooth out sales fluctuations.

* ### Refine Inventory Management

1. **Plan for Seasonality:** Plan for Seasonality: Adjust inventory levels to account for fluctuating demand in Q1, ensuring you are prepared for dynamic sales patterns.
   
2. **Balance Stock for Q2:** Maintain adequate inventory during Q2, where sales trends are more stable, to avoid excess stock or shortages.

* ### Address Regional Differences

1. **Strengthen Marketing in Europe:** Focus on Europe, where sales have been lower, especially in underperforming months. Tailor strategies to regional customer preferences and trends.

2. **Leverage North American Sales:** North America shows strong performance. Maximize this with targeted marketing and optimized inventory.

* ### Improve Product Portfolio

1. **Prioritize Top-Selling Products:** Focus on promoting and optimizing the top 3 products in each category to sustain growth.

2. **Revise Low-Performing Products:** Reassess or phase out bottom-performing products to ensure they don’t drag down overall performance.

</br> 

## SQL queries

```sql
#Load database
USE online_sales;

#Staging table
CREATE TABLE staging_
LIKE `online sales data`;

INSERT INTO staging_
SELECT * FROM `online sales data`;
    
SELECT *
FROM  staging_;

#Standardize data type /field name /spelling
ALTER TABLE staging_
MODIFY COLUMN `date` date;

ALTER TABLE staging_
RENAME COLUMN `Product Category`to product_category;

ALTER TABLE staging_
RENAME COLUMN `Product Name`to product_name;

ALTER TABLE staging_
RENAME COLUMN `Units Sold`to units_sold;

ALTER TABLE staging_
RENAME COLUMN `Unit Price`to unit_price;

ALTER TABLE staging_
RENAME COLUMN `Total Revenue`to total_revenue;

ALTER TABLE staging_
RENAME COLUMN `Payment Method`to payment_method;

ALTER TABLE staging_
RENAME COLUMN `Transaction ID`to transaction_id;

ALTER TABLE staging_
RENAME COLUMN `date`to transaction_date;

UPDATE staging_
SET product_category=TRIM(product_category), Region=TRIM(Region), payment_method=TRIM(payment_method);

#Check duplicate
WITH base AS
(SELECT *,
ROW_NUMBER() OVER (PARTITION BY transaction_id, transaction_date, product_category, product_name, units_sold, unit_price, total_revenue, Region, payment_method) AS row_num
FROM staging_)

SELECT *
FROM base
WHERE row_num >1;

#Top3 & Bottom 3 Product in Category by Revenue
WITH base AS (
    SELECT
        product_category,
        product_name,
        ROUND(total_revenue) total_revenue,
        units_sold,
        ROUND(unit_price) unit_price,
        ROUND((SUM(total_revenue)*100 / (SELECT SUM(total_revenue) FROM staging WHERE product_category = a.product_category)),1) AS product_share,
        DENSE_RANK() OVER (PARTITION BY product_category ORDER BY (SUM(total_revenue)*100 / (SELECT SUM(total_revenue) FROM staging WHERE product_category = a.product_category)) DESC) AS category_rank
    FROM staging_ a
	GROUP BY 1,2,3,4,5
)
SELECT *
FROM base;

##Top3
CREATE VIEW Top3 AS
WITH base AS (
    SELECT
        product_category,
        product_name,
        ROUND(total_revenue) total_revenue,
        units_sold,
        ROUND(unit_price) unit_price,
        ROUND((SUM(total_revenue)*100 / (SELECT SUM(total_revenue) FROM staging WHERE product_category = a.product_category)),1) AS product_share,
        DENSE_RANK() OVER (PARTITION BY product_category ORDER BY (SUM(total_revenue)*100 / (SELECT SUM(total_revenue) FROM staging WHERE product_category = a.product_category)) DESC) AS category_rank
    FROM staging_ a
	GROUP BY 1,2,3,4,5
)
SELECT *
FROM base
WHERE category_rank <=3;

##Bottom3
CREATE VIEW bottom_3 AS
WITH base AS (
    SELECT
        product_category,
        product_name,
        ROUND(total_revenue) total_revenue,
        units_sold,
        ROUND(unit_price) unit_price,
        ROUND((SUM(total_revenue)*100 / (SELECT SUM(total_revenue) FROM staging WHERE product_category = a.product_category)),1) AS product_share,
        DENSE_RANK() OVER (PARTITION BY product_category ORDER BY (SUM(total_revenue)*100 / (SELECT SUM(total_revenue) FROM staging WHERE product_category = a.product_category)) ASC) AS category_rank
    FROM staging_ a
	GROUP BY 1,2,3,4,5
)
SELECT *
FROM base
WHERE category_rank <=3;

#Total Revenue in Month by Category
SELECT
    product_category,
    DATE_FORMAT(transaction_date, '%Y-%m') AS month,
    ROUND(SUM(total_revenue),1) AS total_revenue
FROM staging_
GROUP BY 1,2
order by 1;


SELECT *
FROM staging_;

#Overview KPI
##Total Revenue
SELECT ROUND(SUM(total_revenue),1) total_revenue
FROM staging_;

##AVG Order Value
SELECT ROUND(AVG(unit_price),1) avg_order_unit_price
FROM staging_;

#Revenue by Month & Region
SELECT DATE_FORMAT(transaction_date, '%Y-%m') AS MONTH, ROUND(SUM(total_revenue),1) total_revenue, region
FROM staging_
GROUP BY 1,3;

#Order Volume by Month & Region in Category
SELECT DATE_FORMAT(transaction_date, '%Y-%m') AS month, region, COUNT(transaction_id) AS num_transaction
FROM staging_
group by 1,2
order by 1 ;

```
</details>

---

_[to be reorganized] <Project 4: PV Monitoring Market Analysis>_

<details>

<summary>View Project</summary>

</br>

#Competitive analysis using web analysis tools like Semrush

#Examining competitors through Google Ad Transparency

#Summarizing the competitive analysis findings

#Using the summary as a reference for product development


</details>

---

_[to be reorganized] <Project 3: HR KPI Analysis>_

<details>

<summary>View Project</summary>

</br>

An online project practice that involves working with HR performance data. The goal is to practice creating various charts and visualizations to analyze different data points

#Uncover trends, patterns, and relationships within the attrition rate and job satisfaction rating data.

#Use the data insights to identify potential focus areas for improving HR performance and employee retention.

#Practice translating data-driven findings into actionable recommendations.

</details>

---

_[to be reorganized] <Project 2: Pizza Sales Analysis>_

<details>

<summary>View Project</summary>

</br>

An online project to analyze sales performance. The goal is to use SQL to organize and structure the sales data, and then leverage Tableau to visualize the performance analysis and gain valuable insights.

#Clean, transform, and organize the data to prepare it for analysis.

#Investigate factors that may be influencing sales, such as product performance, customer segments, or regional differences.

#Translate the data-driven findings into actionable recommendations for optimizing product sales and strategies.



</details>

---

## R language ⬇️

_[to be reorganized] <Project 1: Wisconsin Breast Cancer Prediction>_

<details>

<summary>View Code</summary>

```r
## Read the data
data <- read.csv("wisc_bc_data.csv", stringsAsFactors = FALSE)

## View data dimension
dim(data)

## View data structure
str(data)

## Check missing data
sum(is.na(data))

# Load package & pre-process data
library(tidyverse)
data <- select(data, -1) %>%
 mutate_at('diagnosis', as.factor)

## Use stratified sampling
install.packages("sampling")
library(sampling)
set.seed(123)

## Data partitioning, 70% training; 30% testing
train_id <- strata(data, 'diagnosis', size = rev(round(table(data$diagnosis) * 0.7)))$ID_unit
train_data <- data[train_id, ]
test_data <- data[-train_id, ]

## Data modeling
install.packages("caret")
library(caret)
control <- trainControl(method = 'cv', number = 10)
model <- train(diagnosis ~ ., train_data,
              method = 'knn',
              preProcess = c('center', 'scale'),
              trControl = control,
              tuneLength = 5
)

## Result
model[["results"]]

## Model prediction
truth <- test_data$diagnosis
pred <- predict(model, newdata = test_data)
confusionMatrix(table(pred, truth))
