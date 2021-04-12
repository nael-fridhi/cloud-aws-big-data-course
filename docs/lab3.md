# Streaming Analytics using Amazon Kinesis


Log in to the AWS console using the account credientials provided with the lab. Please make sure you are in the us-east-1 (N. Virginia) region when in the AWS console.

1. Create a Kinesis Data Firehose Stream
- In the search bar on top, enter "firehose".
- Select Kinesis Data Firehose from the search results.
- Click Create Delivery Stream.
- Under Delivery stream name, enter "captains-kfh".
- Select Direct PUT or other sources.
- Click Next.
- Click Next.
- Under S3 Bucket, click Create New.
- In S3 bucket name, enter "captains-data" plus some random characters for uniqueness.
- Click Create S3 bucket.
- Click Next.
- Under Buffer size, enter "1".
- Under Buffer interval, enter "60".
- Click Next.
- Click Create delivery stream.


2. Send Data to the Stream

- Open up the send_captains_to_cloud_ file to view the code:
`vi send_captains_to_cloud.py`

- Run the script:
`python send_captains_to_cloud.py`
- Go back to the Kinesis Firehose tab. Our delivery stream should now have a status of Active.

- Click captains-kfh.
- Click the Monitoring tab.
- Switch to a one-hour view by clicking 1h. The graphs may take a few minutes to populate with data points.

Go back to the terminal and stop the script by pressing any key.
Go back to the Kinesis Firehose tab.
Click on Kinesis Firehouse delivery streams and then click on captains-kfh. Alternatively, you can just click on the Details tab.
Under S3 bucket in Amazon S3 destination, click on the link to the bucket to view the S3 bucket.
Click the year folder.
Click the month folder.
Click the day folder.
Click the hour folder to view example objects stored in the bucket.
Go back to the terminal to pull some of the data down.
Find the bucket name:
aws s3 ls
Copy the bucket name.
View the bucket contents:

aws s3 sync s3://<INSERT_BUCKET_NAME> .
View the folder structure:

ll
Change the working directory to the folder for the current year, month, day, and hour:

cd <YEAR>/<MONTH>/<DAY>/<HOUR>/
View the files in the folder:

ll
Copy one of the file names.

View the first 200 characters of the file:

cut -c -200 <INSERT_FILE_NAME>
Return to the home directory:

cd
Start the data-generating stream again:

python send_captains_to_cloud.py
Find the Average Captain Ratings
Go back to the Kinesis Firehose tab.
In the menu on the left, click Data Analytics.
Click Create application.
Under Application name, enter "popular-captains".
Under Description, enter "Our users' favorite captains".
Click Create application.
Click Connect streaming data.
Select Kinesis Firehose delivery stream.
In the Kinesis Firehose delivery stream dropdown menu, select the captains-kfh stream.
Select Choose from IAM roles that Kinesis Analytics can assume.
In the IAM role dropdown menu, select the provided IAM role for this lab.
Click Discover schema.
Click Save and continue.
Under Real time analytics, click Go to SQL editor.
Click Yes, start application.
Create a query using the SQL Editor to show the average rating and total rating of each captain per minute:

CREATE OR REPLACE STREAM "CAPTAIN_SCORES" ("favoritecaptain" varchar(32), "average_rating" DOUBLE, "total_rating" INTEGER);

CREATE OR REPLACE PUMP "STREAM_PUMP" as
INSERT INTO "CAPTAIN_SCORES"
SELECT STREAM "favoritecaptain", avg("rating"), sum("rating")
FROM SOURCE_SQL_STREAM_001
GROUP BY "favoritecaptain", STEP ("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '1' MINUTE)
ORDER BY STEP("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '1' MINUTE), avg("rating");
Click Save and run SQL. The table may take a few minutes to populate.
At the end of the query, change the last line to order the average rating by descending order:

ORDER BY STEP("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '1' MINUTE), avg("rating") desc;
Click Save and run SQL again. The table will need to be recreated, which will take a few minutes before it is ready to view.

Find Anomalous Captain Ratings
On the same SQL editor page, click Download SQL to keep a local copy of the SQL code.
Delete the SQL code for popular captains.
Create a new query that will rank the incoming captain ratings by how anomalous the rating is, displaying the most anomalous values first:

```
CREATE OR REPLACE STREAM "RAW_ANOMALIES" ("favoritecaptain" varchar(32), "rating" INTEGER, "ANOMALY_SCORE" DOUBLE)
CREATE OR REPLACE PUMP "RAW_PUMP" as
INSERT INTO "RAW_ANOMALIES"
SELECT "favoritecaptain", "rating", "ANOMALY_SCORE"
FROM TABLE(
    RANDOM_CUT_FOREST(CURSOR(SELECT STREAM "favoritecaptain", "rating" FROM "SOURCE_SQL_STREAM_001"))
);

CREATE OR REPLACE STREAM "ORDERED_ANOMALIES" ("favoritecaptain" varchar(32), "rating" INTEGER, "ANOMALY_SCORE" DOUBLE)
CREATE OR REPLACE PUMP "ORDERED_PUMP" as
INSERT INTO "ORDERED_ANOMALIES"
SELECT STREAM *
FROM RAW_ANOMALIES
ORDER BY FLOOR("RAW_ANOMALIES".ROWTIME TO SECOND), "ANOMALY_SCORE" desc;
```
- Click Save and run SQL. The table will take a few minutes to populate.