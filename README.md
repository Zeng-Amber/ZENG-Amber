# Hi there, I'm Amber! 👋

I'm a total data junkie who loves diving into business and market intel to uncover insights hidden within the numbers.

👩‍💻 I'm currently working on Market intelligence research related to energy service market for the company I am working on in Munich.

📊 Check out my Tableau profile using SQL work [here](https://public.tableau.com/app/profile/yining.zeng/vizzes).

---

_Projects -Business and market analysis_

### Project 1: HR KPI Analysis

<details>

<summary>View Project</summary>

</br>

[Tableau](https://public.tableau.com/views/HRdashboard_16928327379680/HRanalyticsdashboard?:language=zh-TW&:sid=&:display_count=n&:origin=viz_share_link)

![HR KPI Analysis](https://drive.google.com/uc?export=view&id=1W9K0P3PzDyjoBNMEMW88w0fUbGsBiHms)](https://drive.google.com/drive/u/0/folders/1b_dC6NokoEJxdB3TJg6igCtwemin3iSu)

</details>

---

### Project 2: Pizza Sales Analysis

<details>

<summary>View Project</summary>

</br>

[Tableau](https://public.tableau.com/views/PerformanceofPizzaresaurant/Home?:language=zh-TW&:sid=&:display_count=n&:origin=viz_share_link)

![Pizza Sales Analysis](https://drive.google.com/uc?export=view&id=1ZX97RYpYIFLQ-_MNANtEbPU7Ue0OVeqY)](https://drive.google.com/file/d/1ZX97RYpYIFLQ-_MNANtEbPU7Ue0OVeqY/view?usp=drive_link)

</details>

---

_Projects -R language_

### Project 3: Wisconsin Breast Cancer Prediction

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
