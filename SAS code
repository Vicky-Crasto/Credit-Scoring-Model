# Credit Scoring Model using logistic regression (SAS)

#### Importing data
*Setting the permanent library*
```
libname gd3 "Y:\Vickydec2016\2.Graded Assignment - Logistic Regression"; 
run;
```
*Importing the data set*
```
proc import datafile = "Z:\Assignments\Graded Assignment\Topic 10 - Regression Models\Credit.csv"
out = gd3.credit_data	dbms= csv; 
run;
```

#### Data exploration
```
proc contents data = gd3.credit_data varnum;
run;
```
*Viewing the first 20 observations*
```
proc print data = gd3.credit_data (obs=20);
run;
```
*Finding out the number of missing values in the data*
```
proc means nmiss data = gd3.credit_data1;
run;
```

##### Data Preparation
**Task that need to be done**  
1. Check for missing values and outliers
2. Convert (Income and number of dependents) to numeric from character
3. Create dummy variables for 
	*Education
	*Gender
	*Occupation
    *Region
	*Rented_OwnHouse
4. Understanding the meaning of 1/0 for NPA status

*Converting the character to numeric variables*
```
data gd3.credit_data1;
set gd3.credit_data;
income = monthlyincome*1;
dependents = numberofdependents *1;
run;
```
*Checking for missing values*
```
proc freq data = gd3.credit_data1;
tables income dependents /missing;
run;
```
**_About 19% of the observation have missing values for income  and about 2.3% observation have missing values for no. of dependents_**

**NPA_Status - Understanding the meaning of 1/0 for NPA status**
```
proc univariate data = gd3.credit_data1;
var NPA_Status;
run;
```
**_93% values are indicated as 0 - NPA Status - NO, which mean the customer has not default - a good customer
7% are indicated as 1 - NPA status - yes, which means the customer has default as the % is small._**

#### Missing value treatment
##### 1. NPA Status
*NPA Status is the target variable which cannot be imputed hence the missing values are deleted from the dataset.*
*Identify the number of missing values.*
```
proc print data = gd3.credit_data1;
where npa_status = .;
run;
```

*Deleting the missing values*
```
data gd3.credit_data1;
set gd3.credit_data1;
if npa_status =. then delete;
run;
```
*Checking the variable for missing values*
```
proc freq data = gd3.credit_data1;
tables npa_status / missing;
run;
```

##### Renaming variables 
*Since some of the variable name are very long, they have been renamed*
```
data gd3.credit_data1;
set gd3.credit_data1;
rename RevolvingUtilizationOfUnsecuredL = credit_utiliz;
rename NumberOfTime30_59DaysPastDueNotW = N30_59pastdue;
rename NumberOfOpenCreditLinesAndLoans = credit_lines;
rename NumberOfTimes90DaysLate = N90pastdue;
rename NumberRealEstateLoansOrLines = r_estateloans;
rename NumberOfTime60_89DaysPastDueNotW =N60_90pastdue;
run;
```

##### 2. Credit Utilization 
```
proc contents data = gd3.credit_data1 varnum; run;

proc univariate data = gd3.credit_data1 plot;
var credit_utiliz ; run;
```

**As per the definition  of the Revolving Utilization Of Unsecured Loan, it is ratio of the credit balance (credit used) and the total credit limit, hence intitutively is must be a percentage which ranges from 0 to 100%, where 0% indicate the person has not utilized the credit._**

**The median is about 0.15 but the mean is 6.04, hence this indicates there are large number of outliers on the  higher side**

*Finding out number of observation which are extremely*
```
Proc univariate data = gd3.credit_data1;
var credit_utiliz;
where credit_utiliz >1; run;
```
**About 3321 out 150000 (ie about 2%) values have extreme values or erroneous values These can be either imputed with the mean value or delete from the dataset**
```
Proc univariate data = gd3.credit_data1;
var credit_utiliz;
where credit_utiliz le 1; run;
```
***There about 146676 observation, the mean is 0.3037 and median = 0.1444
Hence, we shall imputed the outlier with mean values and also indicate imputed values as 1 and the rest as 0 in a new variable _credit_uti_impute_***

*Create an indicator variable for credit utilization*
```
data gd3.credit_data2;
set gd3.credit_data1;
if credit_utiliz gt 1 then credit_uti_impute = 1; else credit_uti_impute = 0;
if credit_utiliz gt 1 then credit_utiliznew = 0.3037; else credit_utiliznew = credit_utiliz;run;
```
```
Proc univariate data = gd3.credit_data2;
var credit_utiliznew;  run;
```
```
proc freq data = gd3.credit_data2;
tables credit_uti_impute /missing;  run;
```

##### 3.Age
```
Proc univariate data = gd3.credit_data2;
var age; run;

proc print data = gd3.credit_data2;
where age = 0; run;
```
**_There is one observation with age =0, which will be imputed with average age = 52_**
```
data gd3.credit_data2;
set gd3.credit_data2;
if age = 0 then age_new = 52; else age_new= age;
run;
```

##### 4.N30_59past due
```
proc univariate data = gd3.credit_data2;
var n30_59pastdue; run;
```
```
proc freq data = gd3.credit_data2;
where n30_59pastdue gt 4;
tables n30_59pastdue; run;
```
```
proc freq data = gd3.credit_data2;
tables n30_59pastdue * NPA_status /norow nocol; run;
```

**_Since the data given is for over 2 years, a customer can default a maximum of 24 times, any value above this is erroneous and needs to be checked.
We have cases of 96 and 98 times, which we shall impute with the help of the cross table provide from the above output.
The 98 days slab has 8% of 1s and 10% of 0s, such a break up is also seen in the 5 times slab, which seems to be an appropriate match to impute the values_**
```
data gd3.credit_data2;
set gd3.credit_data2;
if n30_59pastdue gt 90 then n30_59pastdue_new = 5; else n30_59pastdue_new = n30_59pastdue;run;
```
```
proc univariate data = gd3.credit_data2;
var n30_59pastdue_new; run;
```

##### 5.N90pastdue
We shall apply the same logic as impute the erogenous values;
```
proc freq data = gd3.credit_data2;
tables N90pastdue; run;
```
**_We have about 0.18% observations with 98 times defaults which is errorneous, hence we shall impute the values_**
```
proc freq data = gd3.credit_data2;
tables N90pastdue*NPA_Status / nocol norow;  run;
```
**Here the closest match is 4 times**
```
data gd3.credit_data2 (drop = nN90pastdue_new) ;
set gd3.credit_data2;
if N90pastdue gt 90 then N90pastdue_new = 4; else N90pastdue_new = N90pastdue; run;
```
```
proc univariate data = gd3.credit_data2;
var N90pastdue_new; run;
```
```
proc freq data = gd3.credit_data2;
tables N90pastdue_new; run;
```

##### 6. N60_90pastdue;
*We shall apply the same logic as impute the erogenous values*
```
proc freq data = gd3.credit_data2;
tables N60_90pastdue; run;
```
**_We have about 0.18% observations with 98 times defaults which is erroneous, hence we shall impute the values_**
```
proc freq data = gd3.credit_data2;
tables N60_90pastdue*NPA_Status / nocol norow; run;
```
*Here the closest match is 3 times*
```
data gd3.credit_data2;
set gd3.credit_data2;
if N60_90pastdue gt 90 then N60_90pastdue_new = 3; else N60_90pastdue_new = N60_90pastdue;
run;
```
```
proc univariate data = gd3.credit_data2;
var N60_90pastdue_new;
run;
```
```
proc freq data = gd3.credit_data2;
tables N60_90pastdue_new;
run;
```
##### 7. Debt Ratio
```
proc univariate data = gd3.credit_data2;
var debtratio;
where debtratio >2; run;
```
**_As per definition of debt ratio it the ratio of the all the loan payments to gross income. A value higher than 1 indicates that the person has too much loan than his income. Ideally this value must be less than 1.
However, we shall impute the value greater than 2 with the mean value of 0.30_**
```
proc freq data = gd3.credit_data2;
tables debtratio / missing; run;
```
```
data gd3.credit_data2;
set gd3.credit_data2;
if debtratio > 2 then debtratio_impute = 1 ; else debtratio_impute =0;
if debtratio > 2 then debtratio_new =0.30 ; else debtratio_new = debtratio; run;
```
```
proc univariate data = gd3.credit_data2;
var debtratio_new; run;
```

##### 8. Income
**_About 19% of the values are missing, hence we would need to impute these values based on a related value Income can be based on age, gender, occupation, education_**
```
proc univariate data = gd3.credit_data2
var income /missing; run;
```
```
proc freq data = gd3.credit_data2;
where income = .;
tables age_new * education /nocol norow nopercent; run;
```
```
proc freq data = gd3.credit_data2;
where income = .;
tables npa_status /nocol norow nopercent; run;
```

**Creating age groups**
```
proc format;
value agegroup
				low -< 30 = 1
				 30 -< 40 = 2
				 40 -< 50 = 3
				 50 -< 60 = 4
				 60 -< 70 = 5
				 70 -< 80 = 6
				 80 -< 90 = 7
				 90 - high = 8;
value agegroup_label 
				1 = "Upto 30"
				2 = "30 to 40"
				3 = "40 to 50"
				4 = "50 to 60"
				5 = "60 to 70"
				6 = "70 to 80"
				7 = "80 to 90"
				8 = "Above 90";
```
**Create Income groups**
```
value income_groups
				low   -< 2500   = 1
				2500  -< 5000   = 2
				5000  -< 7500   = 3
				7500  -< 10000  = 4
				10000 -< 12500  = 5
				12500 -< 15000  = 6
				15000 -< 20000  = 7
				20000 -< 25000  = 8
				25000 -< high   = 9;
				
value income_lables

				1 = "Upto 2500"
				2 = "2500 to 5000"
				3 = "5000 to 7500"
				4 = "7500 to 10000"
				5 = "10000 to12500"
				6 = "12500 to 15000"
				7 = "15000 to 20000"
				8= "20000 to 25000"
				9 = "25000 to high";
```
**Create credit lines groups**
```				
value credit_lines
                0 - 5 = 1
                5- 10 = 2
                10 - 15 = 3
                15 - 25 = 4
                25 - high = 5;
 value credit_lines_label
                1 =  "0 - 5"
                2= "5- 10" 
                3= "10 - 15" 
                4=  "15 - 25" 
                5= "25 - high" ;
value credit_utiliznew_fmt
                low - 0.15 = 1
                0.15 - 0.30 = 2
                0.30 - 0.45 = 3
                0.45 - 0.60 = 4
                0.60 - 0.75 = 5
                0.75 - 1 = 6
                1- high = 7;
run;
```
*Create new variable indicating the age group*
```
data gd3.credit_data2;
set gd3.credit_data2;
agegroup = put (age, agegroup.);
run;
```
*Converting character to numeric*
```
data gd3.credit_data2;
set gd3.credit_data2;
agegroup1 = agegroup*1;
run;	
```
```
data gd3.credit_data2 (drop = agegroup);
set gd3.credit_data2;
rename agegroup1 = agegroup;
run;
```
```
data gd3.credit_data2;
set gd3.credit_data2;
rename agegroup1 = agegroup;
run;
```

*Inserting row numbers*
```
data gd3.credit_data2;
set gd3.credit_data2;
sr_no = _n_;
run;
```
```
proc sort data = gd3.credit_data2;
by age;
run;		
```
*Cross table age group, education, income*
```
proc tabulate data = gd3.credit_data2;
class agegroup education;
var income;
table agegroup, education*income*mean;
format agegroup $agegroup_label.;
run;
```
Imputing the income basis the age, education as per the cross table create
Creating reference table(in excel) for imputing values and using the same to imputed values;

*Importing reference sheet*
```
proc import datafile ="Y:\Vickydec2016\2.1 Graded Assignment -Regression (reattempted)\Income_impute_reference.csv"
out = gd3.income_impu_ref dbms = csv replace;
run; 
```
*Using infile command to reimport the file since education variable was not imported correctly.*
```
data  gd3.income_impu_ref;
infile 'Y:\Vickydec2016\2.1 Graded Assignment -Regression (reattempted)\Income_impute_reference.csv' delimiter=',' MISSOVER DSD firstobs=2 LRECL=32760;
informat agegroup BEST32.;
informat education $32.;
informat mean_icome BEST32.;
format agegroup BEST12.;
format education $32.;
format mean_icome BEST12.;
label agegroup = 'agegroup';
label education = 'education';
label mean_icome = 'mean_icome';
input    agegroup
education $
 mean_icome;
run;
```
*Creating a new dataset where income is missing*
```
proc sort data = gd3.income_impu_ref;
by agegroup education;  run;
```
```
proc sort data = gd3.credit_data2;
by agegroup  education; run;
```
```
data gd3.credit_data3;
set gd3.credit_data2;
where income = .;run;
```
*Updating the income value using the reference table created*
```
proc sort data = gd3.credit_data3;
by agegroup education; run;
```
*Using merge to update the missing values based on the reference table*
```
data gd3.credit_data3;
merge gd3.credit_data3(in = a) gd3.income_impu_ref (in =b);
by agegroup education;
if a; run;
```
*Dropping extra variables and renaming*
```
data gd3.credit_data3(drop = income income_new);
set gd3.credit_data3; run;
data gd3.credit_data3;
set gd3.credit_data3;
rename mean_icome = income; run;
```
*Update master data set credit_data2 with the imputed values for income from credit_data3*
```
proc contents data = gd3.credit_data2 varnum; run;
data gd3.credit_data2 (drop = income_new);
set gd3.credit_data2; run;
proc contents data = gd3.credit_data3 varnum; run;
```

*Updating the master data file with the imputed values*
```
data gd3.credit_data4;
update gd3.credit_data2 gd3.credit_data3;
by  sr_no; run;
```
*Checking the data after updating*
proc contents data = gd3.credit_data4 varnum; run;
proc univariate data = gd3.credit_data4; run;

##### Treating missing values in no.of dependents

*Checking the number of missing values*
```
proc freq data = gd3.credit_data4;
table dependents /missing; run;
```
```
proc print data =gd3.credit_data4 ;
where dependents ge 4; run;
```
```
proc univariate data = gd3.credit_data4; var income; run;
```
*Creating groups based on income level*
``` sas
proc format;
value income_groups
				low   -< 2500   = 1
				2500  -< 5000   = 2
				5000  -< 7500   = 3
				7500  -< 10000  = 4
				10000 -< 12500  = 5
				12500 -< 15000  = 6
				15000 -< 20000  = 7
				20000 -< 25000  = 8
				25000 -< high   = 9;
value income_lables
				1 = "Upto 2500"
				2 = "2500 to 5000"
				3 = "5000 to 7500"
				4 = "7500 to 10000"
				5 = "10000 to12500"
				6 = "12500 to 15000"
				7 = "15000 to 20000"
				8= "20000 to 25000"
				9 = "25000 to high";
run;
```
*Creating new variable to indicate the income_group and converting it to numeric type*
```
data gd3.credit_data4;
set gd3.credit_data4;
incomegroup = put(income,income_groups.); run;

data gd3.credit_data4; set gd3.credit_data4;
incomegroup1 = incomegroup*1; run;

data gd3.credit_data4; set gd3.credit_data4;
rename incomegroup1 = incomegroup; run;
```
*Cross table - gender\*agegroup\*income\*mean of no. of dependents*
```
proc tabulate data = gd3.credit_data4;
class gender agegroup incomegroup;
var dependents;
table gender*agegroup,
		incomegroup*dependents*mean;
format agegroup agegroup_label.;    
format incomegroup income_lables.;
run;
```
*Cross table of gender\*agegroup\*income \* median of dependents*
```
proc tabulate data = gd3.credit_data4 out = gd3.ref_impute_dependent;
class gender agegroup incomegroup;
var dependents;
table gender*agegroup,
		incomegroup*dependents*median;
*format agegroup agegroup_label.;    
*format incomegroup income_lables.;
run;
```
*Creating reference table using the median values for no. of dependents*
```
proc export data = gd3.ref_impute_dependent
dbms=csv
outfile = "Y:\Vickydec2016\2.1 Graded Assignment -Regression (reattempted)\ref_impute_dependent.csv"
replace; run;
```
*Using reference table create above to impute missing values of no. of dependents*. 
Creating a new dataset where dependents is missing
*Creating indicator variable*
```
 data gd3.credit_data4;
 set gd3.credit_data4;
 if dependents = . then dependent_impute = 1; else dependent_impute = 0;
 dependent_new = dependents;
 run;
 ```
 *Identifying missing values*
 ```
 data gd3.dependent_NA;
 set gd3.credit_data4;
 where dependents =.;
 run;
 ```
 ```
 data gd3.ref_impute_dependent(drop = _TYPE_ _PAGE_ _TABLE_) ;
 set gd3.ref_impute_dependent;
 rename dependents_Median = dependent_new;
 run;
```
```
 data gd3.dependent_NA(drop = dependent_new ) ;
 set gd3.dependent_NA;
 run;
```
*Preparing the data set for imputing*
```
proc sort data = gd3.dependent_NA;
by gender agegroup incomegroup;
run;
```
```
proc sort data = gd3.ref_impute_dependent;
by gender agegroup incomegroup;
run;
```
```
data gd3.dependent_NA_imputed;
merge gd3.dependent_NA (in = a) gd3.ref_impute_dependent (in = b);
by gender agegroup incomegroup;
if a; run;
```
*Updating the master data set with the imputed values for dependent*
```
Proc contents data = gd3.dependent_NA_imputed; run;
proc contents data = gd3.credit_data4; run;
proc sort data = gd3.credit_data4; by sr_no; run;
proc sort data = gd3.dependent_NA_imputed; by sr_no; run;

data gd3.credit_data5;
update gd3.credit_data4 gd3.dependent_NA_imputed;
by  sr_no;
run;
```
**_Credit_data5 is the final data set with correct imputed values_**

*Checking statistics of the tranformed data set*
```
proc means median n nmiss max min mean data = gd3.credit_data5 ; run;
```

##### Creating dummy variables for education, gender, occupation, region, rented_ownhouse
```
data gd3.credit_data6;
set gd3.credit_data5;

Gender dummy variables
if gender = "Male" then gender_dummy = 0; else gender_dummy = 1;

Education dummy variables
if education = "Graduate" then edu_dummy = 1;
else if education = "Matric" then edu_dummy = 2;
else if education = "PhD" then edu_dummy = 3;
else if education = "Post-Grad" then edu_dummy = 4; else edu_dummy = 5;

if education = "Graduate" then edu_grad = 1; else edu_grad = 0;
if education = "Matric" then edu_matric = 1; else edu_matric =0;
if education = "PhD" then edu_phd = 1; else edu_phd = 0;
if education = "Post-Grad" then edu_postgrad = 1 ; else edu_postgrad = 0;

Occupation dummy variable
if occupation = 'Non-offi' then dummy_occup = 1;
else if occupation = 'Officer1' then dummy_occup = 2;
else if occupation = 'Officer2' then dummy_occup = 3;
else if occupation = 'Officer3' then dummy_occup = 4; else dummy_occup = 5;

if occupation = 'Non-offi' then dummy_occup1 = 1; else dummy_occup1 = 0;
if occupation = 'Officer1' then dummy_occup2 = 1; else dummy_occup2 = 0;
if occupation = 'Officer2' then dummy_occup3 = 1; else dummy_occup3 = 0;
if occupation = 'Officer3' then dummy_occup4 = 1; else dummy_occup4 = 0;

House status dummy variable
if rented_ownhouse = "Ownhouse" then dummy_house = 1; else dummy_house= 0;

Region dummy variable
if region= 'Centr' then dummy_region = 1;
else if region= 'East' then dummy_region = 2;
else if region= 'North' then dummy_region = 3;
else if region= 'South' then dummy_region = 4; else dummy_region = 5;

if region= 'Centr' then dummy_centre = 1; else  dummy_centre = 0;
if region= 'East' then dummy_east = 1; else dummy_east = 0;
if region= 'North' then dummy_north = 1; else dummy_north = 0;
if region= 'South' then dummy_south = 1; else dummy_south = 0;
run;
```
##### Understanding relationship between churn and variables

*N60_90pastdue_new Vs churn*
```
proc univariate data = gd3.credit_data6; var r_estateloans  ; run;
```
*NPA status Vs. agegroup*
```
proc freq data =  gd3.credit_data6;
table NPA_Status*agegroup / missing nocol;
format agegroup agegroup_label.;  run;
```
*NPA status Vs. credit lines*
```
proc freq data =  gd3.credit_data6;
table NPA_Status*credit_lines / missing nocol; run;
```
```
proc freq data =  gd3.credit_data6;
table NPA_Status*credit_lines / missing nocol;
format credit_lines credit_lines.;run;
```
*NPA status Vs. Credit uitilization*
```
proc freq data =  gd3.credit_data6;
table NPA_Status*credit_utiliznew / missing nocol;
format credit_utiliznew credit_utiliznew_fmt.;run;
```
*NPA status Vs. debt ratio*
```
proc freq data =  gd3.credit_data6;
table NPA_Status*debtratio_new / missing nocol;
format debtratio_new credit_utiliznew_fmt.; run;
```
```
proc freq data =  gd3.credit_data6;
table NPA_Status*dependent_new / missing nocol;
table NPA_Status*dummy_house / missing nocol;
table NPA_Status*dummy_occup / missing nocol;
table NPA_Status*dummy_region / missing nocol;
table NPA_Status*edu_dummy / missing nocol;
table NPA_Status*gender_dummy / missing nocol;
table NPA_Status*incomegroup / missing nocol;
table NPA_Status*n30_59pastdue_new / missing nocol;
table NPA_Status*r_estateloans / missing nocol;
format incomegroup income_lables.;
format r_estateloans credit_lines.;
run;
```
*Cross table of NPA_Status, incomegroup, dummy_region*
```
proc tabulate data = gd3.credit_data6;
class NPA_Status incomegroup dummy_region;
table NPA_Status*agegroup, incomegroup *(N*f=6.0);
format incomegroup income_lables.;
run;
```
*Cross table of NPA_Status, incomegroup, age group*
```
cross table NPA_Status, incomegroup 
proc tabulate data = gd3.credit_data6;
class NPA_Status incomegroup agegroup;
table NPA_Status*agegroup, incomegroup *(N*f=6.0);
format agegroup agegroup_label.;
format incomegroup income_lables.;
run;
```

#### Create Logistic Regression Model
*Create training and validation data set*
```
data gd3.train_data gd3.valid_data;
set gd3.credit_data6;
random1=ranuni(14345366);
if random1 <0.7 then output  gd3.train_data ; else output gd3.valid_data;
run;
```
*Creating the logistic regression model*

**_Multiple iteration were generated, given before is the best iteration with lowest AUC score_**
```
proc logistic data = gd3.train_data descending outmodel = gd3.train_out;
model NPA_Status =
credit_lines
incomegroup
credit_utiliznew
age_new
n30_59pastdue_new
N90pastdue_new
N60_90pastdue_new
debtratio_new
gender_dummy
dummy_house
edu_matric
edu_phd
edu_postgrad
dummy_occup1
dummy_occup3
dummy_centre
dummy_east
dummy_north
dummy_south / ctable lackfit outroc = gd3.train_roc ;
output out = gd3.train_predicted  p = pred;
score out = gd3.train_score;
run;
```

*Plotting ROC curve *
```
symbol2 i=join v=none c=blue;
proc gplot data = gd3.train_roc;
title "ROC plot";
plot  _SENSIT_ *_1MSPEC_=1/cframe = ligr;
run;
```

*Creating lift chart*
```
proc sort data = gd3.train_score; by P_0; run;
```
```
proc rank data = gd3.train_score
out =gd3.train_gain 
groups = 10
ties = mean;
var P_1 ;
ranks decile; run;
```
*Export train gain to csv*
```
proc export data = gd3.train_gain
outfile = "Y:\Vickydec2016\2.1 Graded Assignment -Regression (reattempted)\train_gain.csv"
dbms = csv replace;
run;
```

*Running the model on the validation dataset*
```
proc logistic inmodel = gd3.train_out;
score data = gd3.valid_data out =gd3.valid_score fitstat;
run;
```

*Checking accuracy*
```
data gd3.valid_testaccu;
set gd3.valid_score;
if F_NPA_Status = 1 and I_NPA_Status = 1 then result = "True Positive";
if F_NPA_Status = 0 and I_NPA_Status = 0 then result = "True Negative";
if F_NPA_Status = 1 and I_NPA_Status = 0 then result = "False Negative";
if F_NPA_Status = 0 and I_NPA_Status = 1 then result = "False Positive";
run;
```
```
proc freq data = gd3.valid_testaccu;
tables result;
run;
```





