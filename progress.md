# Exploratory Data Analysis(EDA) with PySpark on Google Playstore
<p align="center">
<img src="https://github.com/akukudala/Practice_repo/blob/main/PlayStore.png" width="600" height="600"   />
	</p>
	
## Introduction
 This project is a journey of analyzing various apps found on the Google play store with the help of PySpark. 
 Being an everyday phone user, it is interesting  to take the real time application information  and drive the insights on installations.
  
 ## Data Content
 Dataset: Dataset is downloaded from Kaggle
It consists of 28 columns as mentioned below and 450796 rows.

App Name, App Id, Category, Rating, Rating Count, Installs, Minimum Installs,  Free, Price, Currency, Size, Minimum Android, Developer Id, Developer Website  Developer Email, Released, Last update, Privacy Policy, Content Rating, Ad  Supported, In app purchases, Editor Choice, Summary, Reviews, Android version  Text, Developer, Developer Address, Developer Internal ID, Version

## Data Preparation

The data mostly appears to be clean.
	There are some special characters in the data  which needs to be cleaned.
## Goal with EDA
Exploratory Analysis and Visualization
* Top categories in the play store?
* Top apps contains the highest number of installations?
* Ratings structure of the Apps?
* Distribution of App Sizes?
* What % of Apps are updating in a regular basis?
* Free Vs Paid Apps?
* Paid Apps and its top earnings calculated based on installations?

There are around 3 million apps found on Google Play Store. 
With this project, we will analyze various
apps found on the play store with the help of different python libraries.

Let's start the project!

First of all, I am placing the data file in my git repo.
Steps to clean the data and get it ready for the EDA

1. Creating a Spark Session


```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()
```
2. Reading the data from the csv file by providing options to handle multiline data and to consider the first row as header.

```python
df = spark.read.option("wholeFile", True).option("multiline",True).option("header", True).option("inferSchema", "True").csv("Playstore_final_1.csv")
```
3. Using pyspark.sql.DataFrame.printSchema to print the structure in tree format

```python 
df.printSchema()
```

3. In the previous step, we noticed the extra unwanted columns in the dataFrame. We can drop the unwanted columns by using drop() function

```python 
df2=df.drop("_c40","_c29","_c30","_c31","_c32","_c33","_c34","_c35","_c36","_c37","_c38","_c39","_c41","_c42","_c43","_c44","_c45","_c46","_c47","_c48","_c49","_c50","_c51","_c52","_c53","_c54","_c55","_c56")

```
Wohoo! Columns are deleted!

4. Now that we have the correct data, let us check how many records we have in the DataFrame

```python 
df2.count()

```
5. Using cache() and persist() methods, Spark provides an optimization mechanism to store the intermediate computation of an RDD, DataFrame, and Dataset so they can be reused in subsequent actions(reusing the RDD, Dataframe, and Dataset computation result’s).

```python 
df2.cache()
df2.persist()

```
We have our data ready!
<p>Let us look at the Top Category of Apps along with the count </p>

```python 

from pyspark.sql import functions as F
dffinal2.select('Category').groupBy('Category').agg(F.count('Category').alias('CategoryCount')).orderBy('CategoryCount', ascending=False).show()

```
<p>
+-----------------+-------------+<br>
|         Category|CategoryCount|<br>
+-----------------+-------------+<br>
|        Education|        46696|<br>
|            Tools|        33969|<br>
|Books & Reference|        29953|<br>
|  Personalization|        29709|<br>
|    Music & Audio|        28423|<br>
|    Entertainment|        22177|<br>
|        Lifestyle|        18915|<br>
|         Business|        16564|<br>
|     Productivity|        14825|<br>
| Health & Fitness|        14249|<br>
|           Puzzle|        13745|<br>
|   Travel & Local|        12201|<br>
|           Casual|        10767|<br>
|      Photography|        10560|<br>
|           Sports|        10107|<br>
|          Finance|         9326|<br>
|       Simulation|         8917|<br>
| News & Magazines|         8296|<br>
|           Arcade|         8263|<br>
|         Shopping|         7696|<br>
+-----------------+-------------+<br>
only showing top 20 rows <br>
</p>
cache()
<p>root<br>
 <em>|-- App Name: string (nullable = true) <br>
 |-- App Id: string (nullable = true) <br>
 |-- Category: string (nullable = true)<br>
 |-- Rating: string (nullable = true)<br>
 |-- Rating Count: string (nullable = true)<br>
 |-- Installs: string (nullable = true)<br>
 |-- Minimum Installs: string (nullable = true)<br>
 |-- Free: string (nullable = true)<br>
 |-- Price: string (nullable = true)<br>
 |-- Currency: string (nullable = true)<br>
 |-- Size: string (nullable = true)<br>
	----------
|-- _c52: string (nullable = true)
 |-- _c53: string (nullable = true)
 |-- _c54: string (nullable = true)
 |-- _c55: double (nullable = true)
 |-- _c56: double (nullable = tr)</em>
	</p>



https://www.freepnglogos.com/pics/play-store-logo
