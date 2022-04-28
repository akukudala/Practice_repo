## Glue

AWS Glue is a fully managed ETL service that makes it easy to move data between your data stores. AWS Glue simplifies and automates the difficult and time consuming tasks of data discovery, conversion, mapping, and job scheduling. AWS Glue guides you through the process of moving your data with an easy to use console that helps you understand your data sources, prepare the data for analytics, and load it reliably from data sources to destinations.

AWS Glue is integrated with [Amazon S3](https://aws.amazon.com/s3/), [Amazon RDS](https://aws.amazon.com/rds/), and [Amazon Redshift](https://aws.amazon.com/redshift/), and can connect to any JDBC-compliant data store. AWS Glue automatically crawls your data sources, identifies data formats, and then suggests schemas and transformations, so you don’t have to spend time hand-coding data flows. 

A crawler does two things:

 1) discovering data tables in our datastore
2) keeping table schemas up to date.

For this step we are going to create a glue crawler, this will crawl your S3 location and publish data to the glue catalog. We can run the crawler manually or can schedule the crawler if needed by following below steps :


1. Navigate to Glue in the AWS console.
2. Click on Crawlers in the left hand navigation panel.
3. Click on Add crawler 
4. Give Crawler a name
5. Keep the default setting for crawler source type
6. Choose S3 as data store
7. Crawl specified path & include the path name
8. Create an IAM role
9. Select & schedule frequency , on demand or scheduled
10. Click add database.
11. Give database a name and click create. 


References : 

https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html


* * *


