# Georgia-Pacific-Data-Science-Intern-Project
## Internship Project

Designing a complete Machine Learning pipeline and deploying in local production for analysts end users. Building pipeline using Python, Tableau, AWS Sagemaker & AWS S3. Project consisted of working on structured marketing data of ‘Kroger’ and constructing regional prescriptive models (Elastic-Net & Ridge regression) using mix marketing model (MMM). Algorithms helped to analyze pricing and assortment strategy for predicted sales of GP products using pricing elasticity. Used PCA and developed a weekly rolling pricing algorithm in feature engineering. Up to 50 models made in pipeline with R2 score ranging from 0.68-0.94

## Key Technologies used:-

Python (selenium, ETL) ,Alteryx,  AWS Sagemaker, AWS S3, Tableau

## ML Pipeline Architecture

<img src="https://user-images.githubusercontent.com/89546195/222983530-13f7393c-3c21-4598-a28b-e027fb727bed.png" width=80% height=80%>

## Mistake:-
Didn't consider the interaction between GP SKU & competitor. Therefore had to rebuild the pipeline after feature engineering in sagemaker to include the interaction variable. 

## Methodlogy:-

### Goal:- 
To design a Machine learning pipeline (Stratum Regional Models) that can utilize the consumer data that GP buys from Kroger (84.15) for better pricing actions bringing the demographic information into picture. Currently pricing team utilizes the PRISM Model output that are built using  IRI, Nielsen data. There is no demographic information in the model output. The Stratum Regional model will help to assist the PRISM model output by adding an extra layer of demographic information to it. 
### Key Checking parameter: -
To check if the model output (coefficient) is correct, check with existing PRISM model coefficient values for common features.
### Data Collection: -
The Data was automatedly downloaded from Kroger Analytics Platform. The Kroger has two different Data source The Stratum & Market 6. The Stratum has the Demographic data of the customer such as gender, income, age group etc. The Market 6 has the transaction data of product sale such as sales, display %, discounted price etc. I wrote a selenium script in python to download the data (csv) files of each independent features. Total 10 models made. For each model 23 different csv files needed to be downloaded. A Common script with to download the data on local storage.  Data collected from Aug 2016 to 2021 DEC. Covid Effect has a lot of effect on data.
### ETL to AWS S3: -
Using Alteryx environment wrote a ETL script to combine each of the 23 different csv file in to a dataframe. Had to use different joins (left, inner) & to clean data. A special script using Regex to extract SKU and use it to combine the data from stratum & market 6 using common SKU (PPG). Loaded data directly into S3 from Local using Alteryx.
### EDA:- 
In EDA tried to check the different data types, null values, data time format, mean, median, distribution and how does some common factors vary across different region like USA, GA, Tennessee etc. Stored the cleaned dataframe with 1+M rows in S3. A Tableau Dashboard made for basic EDA and comparison study of raw data. 
### Preprocessing:- 
The data for all the different SKUs had different quantity. Some had only a year of Data as the SKU was discontinued whereas some had 3-4 years of data. Had to clean null values (dropping the rows with more than 50% of factors had null values). To impute the missing values had to check if the column had some critical information based on sales missing or the general information of demographic. If had any specific information on demographic missing, then had to impute else dropped the column. 
### Feature Engineering:- 
Some of the key features related to sales were not present in data as the price from Kroger would always be the discounted price, while for pricing analysis base price off the SKU is critical. Had to create a custom Algorithm for calculating the base price of SKU on weekly rolling window. Compared the algorithm result with PRISM values. Based on base price had to calculate discount % and AD %. In EDA using correlation values and VIF score found that the features of  age group and income to be highly correlated therefore had to run a novel method to not only remove collinearity but also reduce the number of feature. As the age group had different age bracket such as 10-20, 20-40, 40-60, 60-80 etc as well as the income group also had different brackets such as 20-30 K, 30-45K, 45- 55 K, 55- 70 K etc. It would have been impossible to combine all this different 8-10 features from just income and age group category and interpret the result for a unit change. Therefore, tried different method to remove collinearity and then combining the feature to more generalized interpretable features such as low age (10-25), medium age (25-55). Started with different data transformation methods such as Log, square root, scaling, and then combining the feature with weighted average value. But the collinearity was not dealt properly therefore had to resort to using PCA for deriving new features. New features had most of the original data property and were not collinear. VIF score of less than 10 achieved.
Week of month was also a key feature. Had to perform Label Encoding to generate the week of Month features.  Final dataframe had 16 different columns and 1 M+ records comprising of a total 450 different SKU. 
### Model building:-
In Model building Process started with basic linear regression to check the coefficient values. Then tried to improve the R2 score using other regression method such as Ridge , Lasso & ElasticNet. The lasso regression was eliminated as some of the coefficient values were reduced to zero. Also it was found that  some of the coefficient had a negative value that was not inline with the industry norm such as the volume of goods sold was negatively correlated with discount %. This was found that as the base price was constant but lack of sell in some weeks led to this issue. Had to handle such data points in dataset for all SKU to improve the fit and coefficient value. Designed a custom algorithm that would remove such datapoints depending on the IQR range and regression coefficient value. Algorithm not only retained the data rather than just dropping  a null value but ran on each different SKU.
Th coefficient values and the actual, predicted volume line were saved in S3 bucket in two different files. A dashboard made using the said values.




