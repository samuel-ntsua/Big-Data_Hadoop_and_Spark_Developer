**Stock Exchange Data Analysis using Big-Data tools such as Hadoop, HIVE and Sqoop.**  

In partial fulfillment of Simplilearn Master Data Science Certification course.  
Due Date: Feb 17 2021  

Project_name: Stock Exchange Data Analysis    

Modeler and presenter: ***Samuel_Y._Ntsua*** 

Trainer and Mentor: ***Ajaykuma*** 

**Description**  

- Objective:  
To use HIVE and Sqoop features for data engineering or analysis and sharing the actionable insights.  

- Problem Statement:  
NewYork stock exchange data of seven years, between 2010 to 2016, is captured for 500+  
listed companies.  
The data set comprises of intra-day prices and volume traded for each listed company.  
The data serves both for machine learning and exploratory analysis projects,  
to automate the trading process and to predict the next trading-day winners or losers.  
The scope of this project is limited to exploratory data analysis.  

- Domain:  
Banking, Financial Services and Insurance (BFSI)
- Content:  
This data set contains prices.csv and securities.csv files having the following features: 

a. Prices.csv:  

Variables                    | Description   
|:-----------------------------|:------------------------------------|  
| Date | Trading date|
| Symbol | Ticker code or listed company code on NY stock exchange  |
| Open | Intra-day opening price for each listed company  |
| Close | Intra-day closing price for each listed company  |
| Low | Intra-day lowest price for each listed company  |
| High | Intra-day highest price for each listed company  |
| Volume | Number of shares traded per day per company  |  

  b. Securities.csv:  

|Variables                    | Description   
|:-----------------------------|:------------------------------------|
| Ticker_Symbol | Country to which the customer belongs
| Security | Legal name of the listed company
| Sector | Business vertical of the listed company
| Sub_Industry| Business domain of the listed company within a Sector.
| Headquarter| Headquarters address

**Steps to perform:**

1) Exploratory analysis to understand how MoM or YoY companies from different sectors  
or industries and states have progressed in a period of 7 years

1) Create a data pipeline using sqoop to pull the data from the table below from MYSQL server into Hive.

2) Create a new hive table with the following fields by joining the above two hive tables.
Please use appropriate Hive built-in functions for columns (a,b,e and h to l).  

|Variables              | Description   
|:----------------------|:------------------------------------|
| Trading_year | Should contain YYYY for each record.|  
| Trading_month| Should contain MM or MMM for each record.|  
| Symbol| Ticker code.|  
| CompanyName| Legal name of the listed company.|  
| State| State to be extracted from headquarters value.|  
| Sector| Business vertical of the listed company.|  
| Sub_Industry| Business domain of the listed company within a sector.|  
| Open| Average of intra-day opening price by month and year for each listed company.|  
| Close| Average of intra-day closing price by month and year for each listed company.|  
| Low| Average of intra-day lowest price by month and year for each listed company.|  
| High| Average of intra-day highest price by month and year for each listed company.|  
| Volume| Average of number of shares traded by month and year for each listed company.|  

**Data Analysis using Hive**  

3) Find the top five companies that are good for investment.  
4) Show the best-growing industry by each state, having at least two or more industries mapped.  
5) For each sector find the following:  

    a. Worst year  
    b. Best year  
    c. Stable year  

[**Part 1 of Proposed Solution:**]()

In the problem Statement, we are given a URL to two csv files for the project; however the wording of the project tasks require learners to use a database called BDHS_PROJECT, which is supposed to be on the LMS server,  to create the data pipeline. But once you log on the LMS server, the supposed database BDHS_PROJECT is not accessible because we are not given the database’s hostname and the authentication information.
Given this ambiguous situation, I am going to upload the two csv files to LMS and created my own database that I can access with my LMS login credentials. This method added extra steps to the data pipeline, which I will describe below:

** From the LMS webconsole prompt:
Log on to mysql server hosted on sqoop server :sqoopdb.slbdh.cloudlabs.com

mysql -h sqoopdb.slbdh.cloudlabs.com -u usrsam -p

(1) database and tables
``` sql 
CREATE DATABASE usrsam;

use usrsam;
```  

``` sql

CREATE TABLE stock_prices (
Trading_date 	date,
Symbol 	varchar(20),
Open 	double,
Close 	double,
Low 	double,
High 	double,
Volume 	int
);

CREATE TABLE stock_companies (
Symbol 	varchar(25),
Company_name 	varchar(120),
Sector 	varchar(80),
Sub_industry 	varchar(80),
Headquarter 	varchar(120)
);
```  


(2) load stock_price and stock_companies into database.

```sql sqoop
LOAD DATA LOCAL INFILE '/mnt/home/usrsam/HDFS_Project1/stock_companies.csv'
INTO TABLE stock_companies
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
;

LOAD DATA LOCAL INFILE '/mnt/home/usrsam/HDFS_Project1/stock_prices.csv'
INTO TABLE stock_prices
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS
;
```  

This question suggests the table BDHS_PROJECT already existed in a MySQL database in the Cloudera Lab environment.  
So we need to check on the table first to make is it there in MySQL and note it's path. Then we can import into Hive.  

Importing into HIVE

``` sql sqoop
sqoop import --connect jdbc:mysql://sqoopdb.slbdh.cloudlabs.com:3306/usrsam \  
--username usrsam \  
-P \  
--table stock_prices \  
--hive-import \  
--create-hive-table \  
--hive-table mysql2hive.stock_prices \  
--num-mappers 1 \ 

```

**Merge and import**  

```sql sqoop
sqoop import --connect jdbc:mysql://sqoopdb.slbdh.cloudlabs.com:3306/usrsam \

--username usrsam \
-P \
--query \
'SELECT stock_prices.*, stock_companies.* \
FROM stock_prices JOIN stock_companies ON (stock_prices.Symbol == stock_companies.Symbol) \
WHERE $CONDITIONS' \

--hive-import \
--create-hive-table \
--hive-table mysql2hive.stock_prices_company_merged \
--num-mappers 1 \
```  
```sqoop
sqoop create-hive-table --connect jdbc:mysql://LH/COMPANY \
--table BDHS_PROJECT --hive-table BDHS_PROJECT \
--username sn \
--password p \
``` 

[**Part 2 of Proposed Solution:**] ()  
