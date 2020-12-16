#### Project Summary

The objective of this project was to create an ETL pipeline for I94 immigration, airports and US demographics datasets to form an analytics database on immigration data. A use case for this analytics database is to find immigration patterns to the US. For example, we could try to find answers to questions such as which days are most popular to immigrate on which airports in the US. 

The project follows the follow steps:

Step 1: Scope the Project and Gather Data
Step 2: Explore and Assess the Data
Step 3: Define the Data Model
Step 4: Run ETL to Model the Data
Step 5: Complete Project Write Up

##### Step 1: Scope the Project and Gather Data
#### Project Scope
To create the analytics database, the following steps will be carried out:

- Use Spark to load the data into dataframes.
- Exploratory data analysis of I94 immigration dataset to identify missing values and strategies for data cleaning.
- Exploratory data analysis of demographics dataset to identify missing values and strategies for data cleaning.
- Perform data cleaning functions on all the datasets.
- Create dimension and fact tables.
+ Create time dimension table from I94 immigration dataset, this table links to the fact table through the arrdate, which is renamed to arrival date,  field.
+ Create usa demographics dimension table from the us cities demographics data. This table links to the fact table through the state code field.
+ Create immigration fact table that has the foreign keys to other fact tables.

The technology used in this project is Apache Spark. Data will be read and staged from the customers repository using Spark.

The project is implemented in immigration.pynb notebook. 

##### Step 2: Explore and Assess the Data

#### Exploratory Data Analysis: 

### Immigration data

#### Data Cleaning steps:
- At a look at what columns have a lot of missing data.
- Drop all columns with over 90% missing values. Columns with over 90% missing values are not deemed to contain sufficient data to be used for analytics.
- Drop all rows with 100% missing values.

            cols  values  % missing values
0             cicid       0          0.000000
1       arrivalPort       0          0.000000
2        state_code      16          5.351171
3       arrivalFlag       0          0.000000
4     departureFlag      13          4.347826
5        updateFlag     299        100.000000
6         matchFlag      13          4.347826
7            gender      35         11.705686
8           airline      12          4.013378
9      flightNumber       5          1.672241
10         visatype       0          0.000000
11             year       0          0.000000
12            month       0          0.000000
13      bornCountry       0          0.000000
14  residentCountry       0          0.000000
15             mode       0          0.000000
16              age       1          0.334448
17             visa       0          0.000000
18        birthYear       1          0.334448
19      arrivalDate       0          0.000000
20    departureDate      15          5.016722

- Look for duplicated in cicid
    No duplicates found in cicid. 
    
### Exploratory Data Analysis:  Airports

- print schema

- look for missing values. Lots of missing values in iata_code and local_code, but they seem important.

 cols  values  % missing values
0          ident       0          0.000000
1           type       0          0.000000
2           name       0          0.000000
3   elevation_ft    7006         12.720835
4      continent       0          0.000000
5    iso_country       0          0.000000
6     iso_region       0          0.000000
7   municipality    5676         10.305946
8       gps_code   14045         25.501589
9      iata_code   45886         83.315479
10    local_code   26389         47.914662
11   coordinates       0          0.000000

- look for only USA values. Still Lots of missing values in iata_code and local_code.
            cols  values  % missing values
0          ident       0          0.000000
1           type       0          0.000000
2           name       0          0.000000
3   elevation_ft     239          1.050226
4      continent       0          0.000000
5    iso_country       0          0.000000
6     iso_region       0          0.000000
7   municipality     102          0.448214
8       gps_code    1773          7.791009
9      iata_code   20738         91.128005
10    local_code    1521          6.683658
11   coordinates       0          0.000000

### Exploratory Data Analysis:  Demographics

- print schema

- look for missing values.

                      cols  values  % missing values
0                     City       0          0.000000
1                    State       0          0.000000
2               Median Age       0          0.000000
3          Male Population       3          0.103770
4        Female Population       3          0.103770
5         Total Population       0          0.000000
6       Number of Veterans      13          0.449671
7             Foreign-born      13          0.449671
8   Average Household Size      16          0.553442
9               State Code       0          0.000000
10                    Race       0          0.000000
11                   Count       0          0.000000

- very few missing values. might be safe to drop the missing values

#### Step 3: Define the Data Model

!(/DATA-MODEL.PNG)

##### Why STAR Schema?
The schema is a STAR schema, as it contains one fact table and 4 dimension tables. The benefits of the STAR schema mainly denormalization, simple queries, fast aggregations will be helpful to our goal of understanding seasonal immigration patterns in the USA across airports, states and visa types. 

##### How will it be helpful?
These are some of the queries that we could run using the STAR schema:

a. Do populated states attract more tourists? 
b. Which region (iso region)  has the highest immigration?
c. Which airport by name sees immigrants of a given visa type. 

The schema has the following tables.
* immigration a fact table
* aiport - This stores details about the airport
* visa - This stores details about various visa types. 
* state_demographics -  This stores details about state population and other demographic information.
* time - This will store deatills about the time of each immigration record broken down into specific units. 

The us demographics dimension table comes from the demographics dataset and links to the immigration fact table at US state level. This dimension would allow analysts to get insights into migration patterns into the US based on demographics as well as overall population of states. We could ask questions such as, do populous states attract more visitors on a monthly basis? We could also create a dashboard to see the patterns in residency of the immigrants based on their i94addr and demographics of states.

The visa type dimension table comes from the immigration datasets and links to the immigaration via the visa_type_key. In future we could details to the visa type dimension to include additional information about the visa. 

The immigration fact table is the heart of the data model. This table's data comes from the immigration data sets and contains keys that links to the dimension tables. The data dictionary of the immigration dataset contains detailed information on the data that makes up the fact table.

### 3.2 Mapping Out Data Pipelines
The pipeline steps are as follows:

Load the datasets
Clean the I94 Immigration data
Create visa_type dimension table
Create time dimension table
Extract clean airport data
Create airport table
Load demographics data
Clean demographics data
Create demographic dimension table
Create immigration fact table


#### Step 4: Run Pipelines to Model the Data

##### Create the data pipelines and the data model
immigration-2 has all the steps to run the above pipeline steps.

##### Include a data dictionary
Included a data dictionary as datadictionary.csv . Apologies for the format, I tried to save it from excel to csv, not sure why the description field is right justified.

##### Run data quality checks to ensure the pipeline ran as expected
The following are some examples of the data quality checks:
 - Data must be a certain size
 - Data must be accurate to some margin of error
 - Data must arrive within a given timeframe from the start of execution
 - Pipelines must run on a particular schedule
 - Data must not contain any sensitive information
 
In the notebook, I have implemented a check to see if the number of rows or number of columns is less than 0 or ot. I also implemented a check to see how many nulls are there in the fact table. If we see null values in the foreign key contriants, then the model is broken, and needs to be fixed.


#### Step 5: Complete Project Write Up


##### What's the goal? What queries will you want to run? How would Spark or Airflow be incorporated? Why did you choose the model you chose?
The goal of the project is to find seasonal patterns in immigration across US airports and states. 

These are some of the queries that we could run using the STAR schema in this project:

a. Do populated states attract more tourists (using only tourist visatype)? 
b. Which region (iso region) has the highest immigration?
c. Which airport (by name) sees highest count of immigrants of a given visa type?
d. Which month has the highest immigration? 
e. Which weekday has the most immigrants in the New York state.

Airflow can used to run the pipelines to move the data from the OLTP database to the schema on a montly basis. 

The schema that I choose is a STAR schema, and it contains one fact table and 4 dimension tables. The benefits of the STAR schema mainly denormalization, simple queries, fast aggregations will be helpful to our goal of understanding seasonal immigration patterns in the USA across airports, states and visa types. 

##### Rationale for the choice of tools and technologies for the project
Apache spark was used because of:
- it's ability to handle multiple file formats with large amounts of data.
- Apache Spark offers a lightning-fast unified analytics engine for big data.
- Spark has easy-to-use APIs for operating on large datasets

##### Propose how often the data should be updated and why.
- The current I94 immigration data is updated monthly, and hence the data will be updated monthly.

##### Post your write-up and final data model in a GitHub repo.
Maybe NA as it have opted of github requirement.

##### Write a description of how you would approach the problem differently under the following scenarios:

###### When the data was increased by 100x, do you store the data in the same way? If your project is heavy on reading over writing, how do you store the data in a way to meet this requirement? What if the requirement is heavy on writing instead?

Right now the project it wroitten in such a way that it reads form the local machine and write back to the local machine. I found Jupyter notebook to be slow even while running this small load. So if the data is increased by 100x, I would move the data to Amazon S3 and move the pipeline to Amazon EMR. The jupyter notebook could be translated to EMR notebook, and mode of Spark would be changed to YARN mode. 

Now, depending on wheather the projct is heavy on reading or writing, we could optimize the table design. Two possible strategies on the basis of requirements are:

* Heavy on reading: Distribution Style - ALL
    Small fact tables could be distributed to All clusters and fact table could be distributed evenly. 
 
* Heavy on writing: Distribution Style - Key
    Rows having similar values are placed in the same slice. That way if some rows need to added to the fact table, they would be easily added to the dimension tables as well to maintain the ACID properties of the database.

Reference: 

1. https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark-performance.html
2. https://aws.amazon.com/blogs/big-data/best-practices-for-successfully-managing-memory-for-apache-spark-applications-on-amazon-emr/


###### How do you run this pipeline on a daily basis by 7 am every day. What if the dag fails, how do you update the dashboard? Will the dashboard still work? Or will you populate the dashboard by using last day?
 
 Apache Airflow will be used to schedule and run data pipelines.  We would add a schedule to a DAG to run daily at 7 am. 
 
 dag = DAG(
        "lesson1.exercise2.1.60",
        start_date=datetime.datetime.now() - datetime.timedelta(days=60),
        schedule_interval='0 7 * * *')
        
 The Airflow server dashboard will automatically say the task failed. One can then go look at the logs of the failed task. 
 
 I would let the failure be visible on the dashboard and not populate the dashboard by using the last day. Since the data is refereshed monthly, it should not make a significant difference.
 
  Reference: https://towardsdatascience.com/apache-airflow-tips-and-best-practices-ff64ce92ef8

###### How do you make your database could be accessed by 100+ people? Can you come up with a more cost-effective approach? Does your project need to support 100+ connection at the same time?.

In this scenario, we would move our analytics database into Amazon Redshift. And create 100 IAM users and create access keys for each use. They can use this access key to conect to the AWS services programatically. Another cost effective approach could be to create an access key for an application that runs in your corporate network and needs AWS access. I don'e think the pplication needs to support 100+ connection at the same time. 

