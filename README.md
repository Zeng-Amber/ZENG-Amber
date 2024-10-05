# Hi there, I'm Amber! 👋

I'm a total data junkie who loves diving into business and market intel to uncover insights hidden within the numbers.

_SQL-Business and market analysis_

### Project 6: Online Retail Shop Performance

<details>
<summary>View Code</summary>

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

### Project 4: PV Monitoring Market Analysis

<details>

<summary>View Project</summary>

</br>

#Competitive analysis using web analysis tools like Semrush

#Examining competitors through Google Ad Transparency

#Summarizing the competitive analysis findings

#Using the summary as a reference for product development


</details>

---

### Project 3: HR KPI Analysis

<details>

<summary>View Project</summary>

</br>

An online project practice that involves working with HR performance data. The goal is to practice creating various charts and visualizations to analyze different data points

#Uncover trends, patterns, and relationships within the attrition rate and job satisfaction rating data.

#Use the data insights to identify potential focus areas for improving HR performance and employee retention.

#Practice translating data-driven findings into actionable recommendations.

</details>

---

### Project 2: Pizza Sales Analysis

<details>

<summary>View Project</summary>

</br>

An online project to analyze sales performance. The goal is to use SQL to organize and structure the sales data, and then leverage Tableau to visualize the performance analysis and gain valuable insights.

#Clean, transform, and organize the data to prepare it for analysis.

#Investigate factors that may be influencing sales, such as product performance, customer segments, or regional differences.

#Translate the data-driven findings into actionable recommendations for optimizing product sales and strategies.



</details>

---

_R language_

### Project 1: Wisconsin Breast Cancer Prediction

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
