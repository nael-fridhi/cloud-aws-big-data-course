# Querying data using Amazon Athena in an S3 bucket

In this lab we'll query data stored in Amazon S3 with SQL queries in Amazon Athena.

0. Create an S3 bucket and put the data from zip file into the bucket.
    - Navigate to s3 and create a bucket
    - unzip the `s3_athena_lab_data.zip` 
    - upload the data to the bucket you just created
1. Create a Table from S3 Bucket Metadata
    - Navigate to Amazon Athena.
    - Click Get Started.
    - Near the top of the page, click Settings.
    - In Query result location, paste in the copied s3 bucket name in the format of s3:///.
    - Click Save.
    - On the left, click Connect data source.
    - Select Query data in Amazon S3 and AWS Glue data catalog.
    - Click Next.
    - Click Add a table and enter schema information manually.
    - Click Continue to add table.
    - Create a new database with the following values, replacing with the previously copied bucket name:
        - Database: aws_service_logs
        - Table Name: cf_access_optimized
        - Location of Input Data Set: s3:///
        - Click Next.
    - In Data Format, select Parquet and click Next.
    - Click Bulk add columns.
    - Paste in the following columns:

        ```time timestamp, location string, bytes bigint, requestip string, method string, host string, uri string, status int, referrer string, useragent string, querystring string, cookie string, resulttype string, requestid string, hostheader string, requestprotocol string, requestbytes bigint, timetaken double, xforwardedfor string, sslprotocol string, sslcipher string, responseresulttype string, httpversion string```

    - Click Add.

    - Scroll to the bottom and click Next.
    Click Add a partition three times.
    - In the first column, set Column Name to "year".
    - In the second column, set Column Name to "month".
    - In the third column, set Column Name to "day".
    - Click Create table.
    - Click Run query. The query should run successfully.

2. Add Partition Metadata
    - Open a new query tab.
    - Clear the sample data and paste in the following query:

        ```MSCK REPAIR TABLE aws_service_logs.cf_access_optimized```
    - Click Run query.
    - Open another query tab.
    - Verify that partitions were created:

        ```SELECT count(*) AS rowcount FROM aws_service_logs.cf_access_optimized```
    - Click Run query. You should see 207535 rows present in the table.

    - Open another query tab.
    - View a sample of our data:

        ```SELECT * FROM aws_service_logs.cf_access_optimized LIMIT 10```
    - Click Run query.

3. Query the Total Bytes Served in a Date Range
    - Open another query tab.
    - View the data within the desired date range:

        ```SELECT * FROM aws_service_logs.cf_access_optimized WHERE time BETWEEN TIMESTAMP '2018-11-02' AND TIMESTAMP '2018-11-03'```
    - Click Run query.
    - Modify the previous query to view the sum of the bytes column:
        ```SELECT SUM(bytes) AS total_bytes FROM aws_service_logs.cf_access_optimized WHERE time BETWEEN TIMESTAMP '2018-11-02' AND TIMESTAMP '2018-11-03'```
    - Click Run query. The value for total_bytes equals 87310409.