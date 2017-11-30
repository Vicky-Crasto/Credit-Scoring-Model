# Credit Scoring Model 

## Project Title 
Build a credit scoring model using logistic regression to identify customers would defaults on their loan payment.
## Problem Statement 
Identify customers would are likely to default on their loan payment basis, in order to help the bank take steps to avoid increase in their NPA levels. 

## Dataset 
Kaggle dataset – “Give Me Some Credit”

## Description of the data 
The dataset consists of about 150000 observations and had 13 variables. The variable can be broadly divided into
1.	Demographic details – Age, number of dependents.
2.	Financial status – Monthly income, debt ratio, utilization of credit lines, no. of lines of credit,
3.	Delinquency history – How many times the user has default in the loan payment in the 30-60 days, 60-90 days and more 90 days past the due date, Serious delinquent (loan defaulter)

## Building the model
The following steps were followed in building the model
1.	Exploring and understanding the data. 
2.	Data preparation.
3.	Model building
4.	Model validation
5.	Interpretation
### 1. Exploring and understanding the data
- Understanding the meaning of each variable.
- Checking the type of the variable (character or numeric)
- Checking for missing values.
- Checking for outliers
- Checking if there are any repeated variables.
- Identifying the target variable to build the model.

We see that two variables monthly income and no of dependents defined as character variables which need to be converted to numeric.
About 19% of the observation have missing values for income and about 2.3% observation have missing values for no. of dependents.
`NPA Status (Serious Dlqin2yrs)` is the target variable which cannot be imputed hence the missing values are deleted from the dataset.
93% values are indicated as 0 - NPA Status - NO, and 7% are indicated as 1 - NPA status – yes.
Intuitively we can see that the bank will have a smaller percentage of defaulter, hence NPA status -yes indicated as 1 represents customers who have defaulted and are bad customers. 
Variables such as `debt ratio`, `RevolvingUtilizationOfUnsecuredL`, `NumberOfTimes90DaysLate`, `NumberOfTime60_89DaysPastDueNotW`, 'NumberOfTime30_59DaysPastDueNotW' have outlier values which needed to be treated.
### 2. Data preparation
#### *Imputation of outliers*
 ##### a.	`RevolvingUtilizationOfUnsecuredL`
As per the definition of the Revolving Utilization Of Unsecured Loan, it is ratio of the credit balance (credit used) and the total credit limit, hence intuitively is must be a percentage which ranges from 0 to 100%, where 0% indicate the person has not utilized the credit.
The median is about 0.15 but the mean is 6.04, hence this indicates there are large number of outliers on the higher side
About 3321 out 150000 (ie about 2%) values have extreme values or erroneous values These can be either imputed with the mean value or delete from the dataset.
We find mean of the observations with value less than 100% and use it to impute the outliers.

##### b.	`NumberOfTime30_59DaysPastDueNotW`
As per the definition of the variable, it indicates in the last two years how many times a customer is fail to pay in 30 to 59 days past the due date of payment. 
Hence  a customer can default a maximum of 24 times, any value above this is erroneous and needs to be checked.
We have cases of 96 and 98 times, which need to impute. 
We create a cross table of the no. of times of default and the event rate of Serious Dlqin2yrs. 
We see that 98 times slab default have a similar event rate as that of 5 times. Hence we imputed the outliers with a value of 5. 
Similarly logic is used for the imputation `NumberOfTimes90DaysLate`, `NumberOfTime 60_89DaysPastDueNotW`.
##### c.	`Age` 
There is one observation with age =0, which will be imputed with average age = 52

##### d.	`Debtratio`  
As per definition of debt ratio it the ratio of the all the loan payments to gross income. A value higher than 1 indicates that the person has too much loan than his income. Ideally this value must be less than 1.
However, we shall impute the value greater than 2 with the mean value of 0.30

##### e.	`Income`
About 19% of the values are missing, hence we would need to impute these values based on a related value Income can be based on age, gender, occupation, education. 
We create new variables such as age group. We create a cross table based on the age group and occupation and find the mean income for each of the combinations.
Based on this cross table, the income variable in imputed based on age and occupation. 

##### f.	`No.of dependents` 
A cross table is created based on age group and income group and the median values of no. of dependent is calculated for each combination. The missing value in this variable are thus imputed basis the age and income of the customer. 

#### Creating dummy variable
Indicator variables are created for gender, occupation, region, house status, education.
#### Building the Logistic Regression model
Divide the data into training and validate dataset
Using NPA Status (Serious Dlqin2yrs) as the target variable we build the logistic regression model. The training data set is used. Multiple iterations were generated, the best iteration with lowest AUC score is selected.
We plot the ROC curve and also generate the lift curve.
The following measure are generated to evaluate the performance of the model 
- Model Fit Statistics – AIC, SC and -2logL
- Concordance table
- Hosmer and Lemeshow test  

Furthermore, we run the model on the testing data to check the accuracy of the prediction. 

Observation, Results and Conclusion have been explained in detail in the presentation [Click here to view](https://github.com/Vicky-Crasto/Credit-Scoring-Model-SAS-/blob/master/Credit%20Scoring%20model%20-%20Results%20%26%20Conclusion.pdf)


