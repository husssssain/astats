# astats




Statistical Analysis Report

1. Business Problem
The primary business problem addressed in this analysis is customer churn in the banking sector. Churn, or customer attrition, refers to when customers stop using a bank’s products or services. Understanding the factors driving churn is crucial, as retaining customers is often more cost-effective than acquiring new ones.

Key Business Question: What factors influence customer churn in the bank's customer base, and how can the bank develop strategies to retain high-value customers?



2. Data Preparation, Sampling, and Cleaning
The dataset used for the analysis contains customer information such as age, gender, credit limits, transaction amounts, and more. The key column indicating churn is the 
Attrition_Flag column, which we encoded into a binary variable:
- 1: Attrited (Churned) Customer
- 0: Existing (Non-Churned) Customer

We cleaned the dataset by removing irrelevant columns (such as customer IDs and pre-calculated classifier outputs) and checked for missing values, confirming that none existed in the data.


# Load necessary libraries
library(ggplot2)
library(dplyr)

# Load the dataset
df <- read.csv("BankChurners.csv")

# Check for missing values
colSums(is.na(df))

# Convert 'Attrition_Flag' into a binary column 'Churn': 1 for 'Attrited Customer', 0 for 'Existing Customer'
df$Churn <- ifelse(df$Attrition_Flag == "Attrited Customer", 1, 0)

# Remove unnecessary columns
df_cleaned <- df %>%
  select(-c(CLIENTNUM, Naive_Bayes_Classifier_Attrition_Flag_Card_Category_Contacts_Count_12_mon_Dependent_count_Education_Level_Months_Inactive_12_mon_1,
            Naive_Bayes_Classifier_Attrition_Flag_Card_Category_Contacts_Count_12_mon_Dependent_count_Education_Level_Months_Inactive_12_mon_2))







3. Exploratory Data Analysis (EDA)

3.1 Distribution of Churned vs Non-Churned Customers

- Observation: The dataset shows that approximately 16% of customers have churned, with the majority (84%) being non-churned customers.




# Plot churn distribution
ggplot(df_cleaned, aes(x = factor(Churn))) +
  geom_bar(fill = c("skyblue", "salmon")) +
  labs(x = "Churn", y = "Count", title = "Distribution of Churned vs Non-Churned Customers") +
  scale_x_discrete(labels = c("Non-Churned", "Churned"))






3.2 Age Distribution by Churn Status

- Observation: Both churned and non-churned customers are evenly spread across different age groups, with no distinct age group more prone to churn.




# Plot age distribution for churned and non-churned customers
ggplot(df_cleaned, aes(x = Customer_Age, fill = factor(Churn))) +
  geom_histogram(position = "identity", alpha = 0.6, bins = 20) +
  scale_fill_manual(values = c("skyblue", "salmon"), labels = c("Non-Churned", "Churned")) +
  labs(x = "Age", y = "Count", title = "Age Distribution by Churn Status") +
  theme_minimal()





3.3 Credit Limit by Churn Status
Credit Limit Boxplot
- Observation: Churned customers tend to have lower credit limits compared to non-churned customers. This suggests that customers with higher credit limits are less likely to churn.




# Boxplot for Credit Limit by Churn status
ggplot(df_cleaned, aes(x = factor(Churn), y = Credit_Limit, fill = factor(Churn))) +
  geom_boxplot() +
  scale_fill_manual(values = c("skyblue", "salmon"), labels = c("Non-Churned", "Churned")) +
  labs(x = "Churn", y = "Credit Limit", title = "Credit Limit by Churn Status") +
  theme_minimal()





3.4 Total Transaction Amount vs. Credit Limit
- Observation: There is a positive relationship between credit limits and total transaction amounts. Non-churned customers tend to have both higher credit limits and higher total transaction amounts.



# Scatter plot of Total Transaction Amount vs Credit Limit
ggplot(df_cleaned, aes(x = Credit_Limit, y = Total_Trans_Amt, color = factor(Churn))) +
  geom_point(alpha = 0.5) +
  scale_color_manual(values = c("skyblue", "salmon"), labels = c("Non-Churned", "Churned")) +
  labs(x = "Credit Limit", y = "Total Transaction Amount", title = "Total Transaction Amount vs Credit Limit") +
  theme_minimal()
  

---

4. Hypotheses to be Tested
To understand the significance of factors related to churn, we tested the following hypotheses:

- Hypothesis 1: There is a significant difference in the credit limits between churned and non-churned customers.
- Hypothesis 2: There is a significant difference in the total transaction amounts between churned and non-churned customers.

---

5. Application of Inferential Statistics

We applied two independent t-tests to examine the differences between churned and non-churned customers in terms of their credit limits and total transaction amounts.

5.1 Hypothesis 1: Credit Limit vs. Churn
Null Hypothesis (H0): There is no significant difference in credit limits between churned and non-churned customers.  
Alternative Hypothesis (H1): There is a significant difference in credit limits between churned and non-churned customers.


- T-statistic: -2.40  
- P-value: 0.016  
-Conclusion: Since the p-value (0.016) is less than the significance level of 0.05, we reject the null hypothesis. There is a significant difference in credit limits between churned and non-churned customers. Churned customers tend to have lower credit limits.


# Perform t-test for Credit Limit between churned and non-churned customers
t_test_credit_limit <- t.test(Credit_Limit ~ Churn, data = df_cleaned)
print(t_test_credit_limit)





5.2 Hypothesis 2: Total Transaction Amount vs. Churn
Null Hypothesis (H0): There is no significant difference in total transaction amounts between churned and non-churned customers.  
Alternative Hypothesis (H1): There is a significant difference in total transaction amounts between churned and non-churned customers.


- T-statistic: -17.21  
- P-value: ~0 (1.86e-65)  
- Conclusion: Since the p-value is extremely small, we reject the null hypothesis. There is a significant difference in total transaction amounts between churned and non-churned customers. Churned customers tend to have significantly lower transaction amounts.


# Perform t-test for Total Transaction Amount between churned and non-churned customers
t_test_trans_amt <- t.test(Total_Trans_Amt ~ Churn, data = df_cleaned)
print(t_test_trans_amt)







6. Interpretation of Results
The results from the statistical tests highlight two important factors influencing customer churn:

- Credit Limits: Churned customers tend to have lower credit limits, which could indicate that customers with higher spending potential are more engaged and less likely to leave.
- Transaction Amounts: Churned customers exhibit lower total transaction amounts, suggesting that lower engagement and spending might be early indicators of churn risk.

7. Data-Driven Recommendations
Based on the analysis, the following recommendations can be made to reduce customer churn:
- Increase Engagement with Low Credit Limit Customers: Customers with lower credit limits are at higher risk of churn. The bank should focus on strategies to increase engagement and provide tailored financial products to retain these customers.
- Monitor Transaction Activity: Customers with decreasing transaction amounts should be identified early. Implementing loyalty programs or offering incentives to increase spending activity could help prevent churn.

---

8. Limitations and Future Work
- Limitations: The analysis is based on historical data and does not account for external factors, such as changes in the bank's services or market conditions, which could also influence churn. Additionally, some assumptions of normality in the data may not hold, although the large sample size mitigates this.
- Future Work: Future analysis could incorporate predictive modeling, such as logistic regression, to identify churn more accurately. Moreover, segmenting customers based on additional behavioral factors (e.g., product holding patterns) could provide further insights into personalized retention strategies.

---

9. GitHub Repository
https://github.com/husssssain/astats

---

