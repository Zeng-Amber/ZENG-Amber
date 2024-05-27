# Hi there, I'm Amber! 👋

I'm a total data junkie who loves diving into business and market intel to uncover insights hidden within the numbers.

👩‍💻 I'm currently working on Market intelligence research related to energy service market for the company I am working on in Munich.

📊 Check out my Tableau profile using SQL work [here](https://public.tableau.com/app/profile/yining.zeng/vizzes).


_Projects -Business and market analysis_

### Project 5: Market Research Review

<details>

<summary>View Project</summary>

</br>

*In my role executing market research, I have conducted market intelligence research to provide insights within the department.*

#Competing Market and Product Trend
![Competing Market and Product Trend](https://drive.google.com/uc?export=view&id=1Oazc-Iuuf29a77a6XT1TNSAsA_GUi6kH)

#Lab Power Supply Market Trend
![Lab Power Supply Market](https://drive.google.com/uc?export=view&id=1RvZe0peTLvv4BmNm_ZenSpo_G11YJKdm)

#Wieland and Schuko Product
![Wieland and Schuko](https://drive.google.com/uc?export=view&id=1GhoXTTHl6aUI7ck_lwiEHCYpO14ynb3h)

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

[Tableau](https://public.tableau.com/views/PVmonitorresearch/Dashboard2?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

![PV Monitoring Market Analysis](https://drive.google.com/uc?export=view&id=1Oi0kaX29C42XdnWSgZPiMSglfv52DjgJ)
![Wieland and Schuko](https://drive.google.com/uc?export=view&id=12fJ5i13HKRBHcvNEE9FTSTWZEY7Cq7EN)

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

[Tableau](https://public.tableau.com/views/HRdashboard_16928327379680/HRanalyticsdashboard?:language=zh-TW&:sid=&:display_count=n&:origin=viz_share_link)

![HR KPI Analysis](https://drive.google.com/uc?export=view&id=1W9K0P3PzDyjoBNMEMW88w0fUbGsBiHms)

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

[Tableau](https://public.tableau.com/views/PerformanceofPizzaresaurant/Home?:language=zh-TW&:sid=&:display_count=n&:origin=viz_share_link)

![Pizza Sales Analysis](https://drive.google.com/uc?export=view&id=1ZX97RYpYIFLQ-_MNANtEbPU7Ue0OVeqY)

</details>

---

_Projects -R language_

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
