# Mortgage-Default-Predictions
Using ML methods to predict mortgage defaults. Base data was released Fannie Mae Single Family Loans

# Overview #
The goal of this project was to predict whether or not a mortage will default given a number of features relevent to the current state of the loan and the borrower. The base data collected was all released single family mortage data from Fannie Mae. (you can find this data here https://capitalmarkets.fanniemae.com/credit-risk-transfer/single-family-credit-risk-transfer/fannie-mae-single-family-loan-performance-data). This orginally data totaled over 400gb in size and in 80+ different excel files. I formatted data into three tables and moved to SQLite database for ease of analysis. 

## Data Processing ##
I pulled data equally across the 20 years of available data to avoid any macro econonmic trends that may bias my model. In total there were almost 500k unique mortages with monthy updates through 2022. The original dataset includes 108 features per entry. You can find the full descriptions of these here: https://capitalmarkets.fanniemae.com/media/6931/display. I defined loans as being default (in my case high risk of defualt) if they were at any point 3 or more months deliquent. At that point in my data a high percentage of those mortgages experience forclosure, sale, or other credit events. Looking back to historical data a very small percentage make it to full term of the loan. So those mortages were defined as default for my dataset. I defined safe mortgages as 90%+ paid off. I did not use the full 108 features as many had an overwhelmingly large amount of missing values, or simply not relevent by my assumtions. I decided on 18 features for my modeling, four of which were engineered via the orginal features:

<img width="756" alt="features" src="https://github.com/JoeyNiestroy/Mortgage-Default-Predictions/assets/106636917/c6a3498a-cd99-4f12-a4dc-625e8bb9b025">

The four engineered features are as follows: 

_State_YoY_Change_ : Variable defined by whether a State's rates of defaults in the previous year was statstically different from the rest of the country (1:<,0:=,2:>)

_Pct_left_UPB_ : The percent of the loan left to be paid

_DLQ_STATUS_FLAG_ : A binary variable defined by whether or not the borrower has already missed a payment

_CREDIT_DROP_FLAG_ : A binary variable defined by whether or not the borrower's credit score has dropped over the course of the loan

All other features were left unchanged from the orginal dataset. Any categorial features were simply one-hot encoded.

The final dataset used in modeling removed all monthly samples with a loan delinquent > 3 months as these loans will have already been defined as default, and any mortgage that does not fit requirements for defualt or safe. Train and test split was 80/20 and data was split along unique loans to ensure zero information leak in the test data

**Final num of monthy samples:** 10M+

**Final num of sample with removed nan values:** 5M+

**Percent of positive samples in training data (Defaulted Loans):** 50%ish


## Modeling and Results ##

For the purpose of predicting defaults I stuck to two model. Basic logistic regression and XGBoosted trees. I would have liked to explore a wider range of models and done more in depth hyperparamter tuning but I'm limited in compute. Xgboost handles nan values in features which allowed it to be trained off almost double the amount of data. 
The following results are from the 20% clean test data:

Model Type    |  Accuracy    | Precision-Neg | Precision-Pos | Recall-Neg | Recall-Pos
------------- | -------------| ---------     | ---------     | ---------- | --------- |
Logistic Regression  | 93%     | 93%           |    94%       |  97%       |   86% 
XGBoost  |     93%       |    91%            |   97%        |  98%     | 88%
Random Forest  |     92%       |    90%            |   97%        |  99%     | 80%

Overall XGBoost performs slightly better across the board and that model still has not been optmized through any form of grid searching so there could be far more performance left on the table. The tree models allow an interesting insights to feature importance; with interest rates, credit scores, and OLTV ranking very high. While surprisingly States and Sellers trended on average far lower.

I'd love to simulate the potential costs/margins changes if this model was implemented but it estimating recovery value from foreclosure and effectiveness of payments plans is comp expensive for 400gb+ of data so I'll update when it actually finishes running.  

## Further Research ##
I'd like to expand my EDA of the data and maybe do a basic write up on the performance of certain mortgage sellers and whether certains states are moving one direction or another in terms of loan safety.
