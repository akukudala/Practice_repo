# Amazon S3 & Athena - Interact, Encrypt and Query

https://www.google.com/imgres?imgurl=https%3A%2F%2Fwww.holistics.io%2Fblog%2Fcontent%2Fimages%2F2019%2F03%2Fathena-header.PNG&imgrefurl=https%3A%2F%2Fwww.holistics.io%2Fblog%2Fconnecting-aws-athena-to-query-csv-data-in-s3-with-sql-and-holistics%2F&tbnid=-DC7QILZ3LUcHM&vet=12ahUKEwjsqbWcvLz3AhVkrHIEHXe6B1AQMygKegUIARDLAQ..i&docid=QhwyYQ1mA_h_4M&w=656&h=445&q=s3%20bucket%20and%20Athena&ved=2ahUKEwjsqbWcvLz3AhVkrHIEHXe6B1AQMygKegUIARDLAQ#imgrc=I2O_2mMe9XUL0M&imgdii=F0H4Lv__oAexlM
### Introduction

<p>Amazon Simple Storage Service (Amazon S3) is an object storage service that offers industry-leading scalability,
data availability, security, and performance. This means customers of all sizes and industries can use it to store and protect
any amount of data for a range of use cases, such as data lakes, websites, cloud-native applications, backups, archive,
 machine learning, and analytics.
Amazon S3 stores data for millions of customers all around the world. Data on Amazon S3 is 
spread across multiple devices and availability zones within a region automatically.
Amazon S3 provides a storage management and administration capability that is highly flexible.
S3 provides various data protection requirements which helps application owners keep their data secure at all times. </p>
_But, does everyone know?_
<p>Although s3 has been across the most vulnerable AWS service due to misconfiguration by the application owners, 
if configured as per security best practices, S3 can be used as a most reliable data lake because it can be easily integrated with data analytic tools (Ex: Athena, quick sight). </p>
In this article, let us explore how our bigdata can be securely maintained in S3. This documentation has sample code which can be considered as a beginner guide to interact with AWS service Amazon S3. This article will also provide details on how to create a bucket, loading data file, to enable encryption and how to use Athena to interact with S3.

# Table of contents

1. [Introduction to AWS Amazon S3](#store1)

    1.1 [How does Amazon S3 store data?](#store)</br>
    1.2 [Interacting With AWS Services](#interactaws)</br>
    1.3 [Interacting with S3](#interacts3)</br>
    1.4 [Creating an S3 bucket](#creates3)</br>
    1.5 [Listing all the buckets in you AWS Accounts](#listbuckets)</br>
 
 2. [Load data into a Bucket](#bucket1)

    2.1 [Uploading CSV File into S3 Bucket](#csv)</br>
    2.2 [Listing Objects in S3 Bucket](#listbucketss3)</br>
       
 3. [Encrypting the data](#encrypt1)

    3.1 [Enabling Encryption on S3 Bucket & getting confirmation](#encrypt2)</br>
    3.2 [Get Details of Encryption](#encrypt3)</br>

 4. [Using Athena to interact with S3 objects](#athena1)
    
    4.1 [Athena Introduction](#athena2)</br>
    4.2 [create table](#athena3)</br>
    4.3 [query the desired data](#athena4)</br>
 5. [Conclusion](#conclusion)</br>
 6. [Sources](#sources)</br>


## 1. Introduction to AWS Amazon S3 <a name="store1"></a>
### 1.1 How does Amazon S3 store data? <a name="store"></a>
The Amazon S3 stores data as objects within buckets. An object consists of a file and optionally any metadata that describes that file. 
To store an object in Amazon S3, the user can upload the file that he/she wants to store in the bucket.
<p>A bucket is a container (web folder) for objects (files) which performs a Persisting function 
(Persisting refers to any time data is written to non-volatile storage, including but not limited to writing the data to back-end storage, 
shared/cloud storage, hard drive, or storage media). Every Amazon S3 object is contained in a bucket. 
Buckets form the top-level namespace for Amazon S3, and bucket names are global. 
This means that bucket names must be unique across all AWS accounts, 
much like Domain Name System (DNS) domain names, not just within your own account.
</p>

### 1.2 Interacting With AWS Services  <a name="interactaws"></a>

* We can interact with AWS services either through console or through AWS CLI. In this scenario, we will interact with S3 using AWS CLI.console. 

![](https://github.com/akukudala/homework_603/blob/main/S2Cli.png)


### 1.3 Interacting with S3 <a name="interacts3"></a>

* Before even we interact with S3 using CLI, we would require IAM user credentials.
* In AWS management console, click on IAM and create a new user
* Add a new user with programmatic access and attach the permission policies you need for s3 and Athena
* The policy you'll need is Amazon Athena full access or a custom policy with full
access to Athena and lists read/write permissions to the source S3 bucket
More information can be found in the below link:
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console

### 1.4 Creating an S3 bucket : <a name="creates3"></a>

* Once we have configured the CLI with AWS credentials, we will now create an S3 bucket. 
* Command - aws s3api create-bucket —bucket bucket name 
* Here, we are creating aks_s3 bucket. While choosing the region, we can select the Region closest to where our clients will be accessing or is located.

```
 Data_lake % aws s3api create-bucket --bucket aks_s3 --region us-east-1
{
    "Location": "/aks_s3"
}
```

###  1.5 Listing the buckets in you AWS Accounts <a name="listbuckets"></a>

* Command - aws s3 ls can be used to list out all the buckets in our AWS account.

```
aws s3 ls                                                                       
2022-03-31 12:29:02 aks_s3
2022-03-31 12:28:19 akshitha-test
```
## 2. Load data into a Bucket  <a name="bucket1"></a>
### 2.1 Uploading CSV File into S3 Bucket <a name="csv"></a>

* Command - aws S3 cp can be used to upload files into specific s3 buckets , in the below code we are uploading files into aks_s3 s3 bucket which we created in the previous step.
```
aws s3 cp DataBreaches\(2004-2021\).csv s3://aks_s3
upload: ./DataBreaches(2004-2021).csv to s3://aks_s3/DataBreaches(2004-2021).csv
```
* We uploaded a CSV file in this example, take note of the column names and data types in the table.
* Set the permissions and properties you need.
* Before heading to AWS Athena from AWS management console, we shall enable the encryption.
* All the objects in S3 can be listed using aws s3 ls bucket-name command.

```
aws s3 ls s3://aks_s3                           
2022-03-31 12:36:54      16665 DataBreaches(2004-2021).csv
```
## 3. Encrypt the data <a name="encrypt1"></a>

### 3.1 Enabling Encryption on S3 Bucket & getting confirmation <a name="encrypt2"></a>

* Data security is most important aspect when it comes to handling critical data [PII etc].
* Command - aws s3api put-bucket-encryption —bucket bucket-name is used to enable default encryption on S3 bucket. 

```
aws s3api put-bucket-encryption --bucket aks_s3 --server-side-encryption-configuration '{"Rules": [{"ApplyServerSideEncryptionByDefault": {"SSEAlgorithm": "AES256"}}]}'
```


### 3.2 Get Details of Encryption <a name="encrypt3"></a>

```
aws s3api get-bucket-encryption --bucket aks_s3
{
    "ServerSideEncryptionConfiguration": {
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "AES256"
                },
                "BucketKeyEnabled": false
            }
        ]
    }
}
```



## Athena 

Amazon Athena is a service that makes it easy to create analyze data in Amazon S3 using open standards. Athena is serverless, so there is no infrastructure to manage, and you pay only for the queries that you run. Athena is easy to use. Simply point to your data in Amazon S3 and start querying using standard SQL. Most results are delivered within seconds. With Athena, there’s no need for complex ETL jobs to prepare your data for analysis. This makes it easy for anyone with SQL skills to quickly analyze large-scale datasets. 

## 4. Using Athena to interact with S3 objects<a name="athena1"></a>

### 4.1 Athena Introduction <a name="athena2"></a>
Athena is serverless interative query system. It works on data stored in S3 and uses SQL for querying data. It also supports variety of formats like csv, json, Avro, ORC (columnar) and Parquet (columnar) which are recommenble in Bigdata storage. 
Only pay for querying the data. It is $5 per TB data scanned. No charge for DDL and failed Queries.
One of the best ways to save the cost, we can create Columnar formats (Parquet and ORC) and by using Partitions
Quickly queries Unstructured, Semi Structured and Structured Data. Uses Presto (It is a distributed SQL query engine for BigData).
Different ways to access the Amazon Athena are
* AWS Console
* Athena API
* Athena CLI
* JDBC Connection
It integrates with AWS Glue data catalog.
Integrates with Quicksight (BI tool) for data visualization.

 Why Athena got Popular?

Used by Data Analysts to query large S3 data sets
No need to spinup servers or Hadoop Clusters
 Athena Usescases
Analyzing CloudTrail/CloudFront/VPC/ELB logs
Integration through ODBC/JDBC with other visualization tool
Ad-hoc logs analysis

How to create a table in Athena?


```CREATE EXTERNAL TABLE 'TABLE_NAME'
('COLUMNNAME_1' DATATYPE ,------,'COLUMNNAME_N' DATATYPE) 
LOCATION 'S3://LOCATION//'  
```


Create a new database (if you have not set up one)
Give your table a name and add your path inside the S3 bucket and folder
Indicate data format as CSV and add the column names and data types using bulk-add option for your table.
### 4.2 create table <a name="athena3"></a>

* Go to athena service through AWS management console & click on explore query editor to the right.
* As shown in the below image click on create table from S3 bucket data source

![](https://github.com/akukudala/homework_603/blob/main/Screen%20Shot%202022-03-31%20at%203.26.15%20PM.png)


* Select the input location of dataset,  this would be the s3 location/bucket-name as shown in the below image. Athena provide a browse s3 functionality which simplifies locating the s3 bucket. 
* Once all the required fields are filled out click on create table.

![](https://github.com/akukudala/homework_603/blob/main/Screen%20Shot%202022-03-31%20at%203.57.32%20PM.png)

### 4.3 query the desired data <a name="athena4"></a>
* Once tables are created successfully we can query the desired data, below are few examples :

![](https://github.com/akukudala/homework_603/blob/main/Screen%20Shot%202022-03-31%20at%206.13.27%20PM.png)
![](https://github.com/akukudala/homework_603/blob/main/Screen%20Shot%202022-03-31%20at%206.15.56%20PM.png)
## 5. Conclusion <a name="conclusion"></a>
test data

## 6. Sources <a name="sources"></a>

* Amazon S3 :https://aws.amazon.com/pm/serv-s3/?trk=fecf68c9-3874-4ae2-a7ed-72b6d19c8034&sc_channel=ps&sc_campaign=acquisition&sc_medium=ACQ-P|PS-GO|Brand|Desktop|SU|Storage|S3|US|EN|Text&s_kwcid=AL!4422!3!488982706722!e!!g!!s3&ef_id=CjwKCAjwopWSBhB6EiwAjxmqDVZhQqzk-utK6i34xptNzA7MVWoo_nRYSj5jfzxiuxaCc1qt1MLokBoCMnsQAvD_BwE:G:s&s_kwcid=AL!4422!3!488982706722!e!!g!!s3
* Athena : https://aws.amazon.com/athena/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc
* Creating IAM User/Roles : https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console
* AWS CLI : https://docs.aws.amazon.com/cli/latest/reference/s3api/index.html
* We  can use the Amazon [S3 Bucket Keys](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-key.html) feature to reduce cost/scaling issues
* https://www.youtube.com/watch?v=8VOf1PUFE0I
