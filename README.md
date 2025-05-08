# supermarket-abm-layout
A project using agent-based modeling and R data analysis to optimize supermarket layouts for healthier purchasing behavior.
[Team 3.pdf](https://github.com/user-attachments/files/20105611/Team.3.pdf)
# data_cleaning.R
# Cleaning and summarizing supermarket transactional data
# Project: Optimizing Supermarket Layout with Agent-Based Models

# Load required packages
library(dplyr)
library(readr)
library(lubridate)

# Load data (example path - update if needed)
sales_data <- read_csv("data/raw/transactions.csv")

# View basic structure
glimpse(sales_data)

# --- Step 1: Clean column names ---
colnames(sales_data) <- tolower(gsub(" ", "_", colnames(sales_data)))

# --- Step 2: Handle missing or invalid values ---
sales_data_clean <- sales_data %>%
  filter(!is.na(product_id), !is.na(transaction_id)) %>%
  mutate(purchase_date = ymd(purchase_date),
         quantity = ifelse(quantity < 0, NA, quantity),
         price = ifelse(price <= 0, NA, price)) %>%
  drop_na()

# --- Step 3: Feature engineering ---
sales_data_clean <- sales_data_clean %>%
  mutate(total_value = quantity * price)

# --- Step 4: Aggregate daily sales ---
daily_sales <- sales_data_clean %>%
  group_by(purchase_date) %>%
  summarise(
    total_revenue = sum(total_value, na.rm = TRUE),
    total_items = sum(quantity, na.rm = TRUE),
    num_transactions = n_distinct(transaction_id)
  )

# Save cleaned and summary data
write_csv(sales_data_clean, "data/processed/sales_cleaned.csv")
write_csv(daily_sales, "output/daily_sales_summary.csv")

# --- Optional: Simple plot ---
library(ggplot2)
ggplot(daily_sales, aes(x = purchase_date, y = total_revenue)) +
  geom_line(color = "steelblue") +
  labs(title = "Daily Revenue Trend", x = "Date", y = "Revenue (£)")
supermarket-abm-layout/
└── code/
    └── data_cleaning.R
## 📂 Scripts Included
- `code/data_cleaning.R`  
  An R script that demonstrates a real-world data preprocessing pipeline:
  - Cleans and standardizes supermarket transaction data
  - Handles missing values and invalid entries
  - Aggregates daily sales data and generates simple trend plots
  - Uses `dplyr`, `lubridate`, and `ggplot2` for analysis
