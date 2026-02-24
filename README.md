# Hadoop-and-Hive

## Business Use Case: Telecommunication Data Analytics with Hive Partitioning and Bucketing
```
Problem Statement
A telecommunication company processes vast amounts of data related to customer calls, SMS usage, and data consumption. This data is used to analyze telecom usage trends, such as:
1. Call Durations by region and date.
2. Top Data Users in specific regions.
3. SMS usage trends based on customer demographics.

Given the large scale of the dataset (millions of records), querying the data for business insights is slow without proper optimization. The company wants to:
- Optimize queries to retrieve data based on call date and region for reporting purposes.
- Speed up queries to identify heavy data users.
- Use partitioning and bucketing to manage the dataset efficiently in Hive, improving query performance and storage optimization.
Sample Data
Call Data Table (call_data)
Columns:
- call_id: INT
- customer_id: INT
- call_duration: FLOAT
- region: STRING
- call_date: DATE

Sample Data:
1, 101, 15.5, North, 2023-08-01
2, 102, 20.2, South, 2023-08-02
3, 103, 5.7, East, 2023-08-03
4, 104, 12.4, West, 2023-08-04
5, 105, 25.0, North, 2023-08-05
Data Usage Table (data_usage)
Columns:
- usage_id: INT
- customer_id: INT
- data_used: FLOAT (in GB)
- region: STRING
- usage_date: DATE

Sample Data:
1, 101, 2.5, North, 2023-08-01
2, 102, 3.0, South, 2023-08-02
3, 103, 1.2, East, 2023-08-03
4, 104, 5.5, West, 2023-08-04
5, 105, 10.0, North, 2023-08-05
SMS Data Table (sms_data)
Columns:
- sms_id: INT
- customer_id: INT
- sms_count: INT
- region: STRING
- sms_date: DATE

Sample Data:
1, 101, 5, North, 2023-08-01
2, 102, 10, South, 2023-08-02
3, 103, 8, East, 2023-08-03
4, 104, 7, West, 2023-08-04
5, 105, 15, North, 2023-08-05
```


## Solution: Using Partitioning and Bucketing in Hive for Telecom Data
```
1. Partitioning
Partition the data by call_date and region. This helps reduce the amount of data scanned when querying for specific dates or regions.
•	- Partition on: call_date, region
2. Bucketing
Use bucketing on customer_id for all tables (call_data, data_usage, sms_data). This allows for efficient queries targeting specific customers.
•	- Bucket on: customer_id
3. Create Table with Partitioning and Bucketing
Hive Query to create the partitioned and bucketed table for call data, data usage, and SMS data:
CREATE TABLE call_data (
    call_id INT,
    customer_id INT,
    call_duration FLOAT
)
PARTITIONED BY (call_date STRING, region STRING)
CLUSTERED BY (customer_id) INTO 10 BUCKETS
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS ORC;

CREATE TABLE data_usage (
    usage_id INT,
    customer_id INT,
    data_used FLOAT
)
PARTITIONED BY (usage_date STRING, region STRING)
CLUSTERED BY (customer_id) INTO 10 BUCKETS
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS ORC;

CREATE TABLE sms_data (
    sms_id INT,
    customer_id INT,
    sms_count INT
)
PARTITIONED BY (sms_date STRING, region STRING)
CLUSTERED BY (customer_id) INTO 10 BUCKETS
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS ORC;
4. Load Data into Partitioned and Bucketed Tables
When loading data, specify the partition values:
LOAD DATA INPATH '/path/to/call_data_2023-08-01_North.csv'
INTO TABLE call_data
PARTITION (call_date='2023-08-01', region='North');

LOAD DATA INPATH '/path/to/data_usage_2023-08-01_North.csv'
INTO TABLE data_usage
PARTITION (usage_date='2023-08-01', region='North');

LOAD DATA INPATH '/path/to/sms_data_2023-08-01_North.csv'
INTO TABLE sms_data
PARTITION (sms_date='2023-08-01', region='North');
5. Query Optimizations Using Partitioning and Bucketing
a. Call Durations by Region and Date
To query total call durations for a specific region and date:
SELECT SUM(call_duration) AS total_duration
FROM call_data
WHERE call_date = '2023-08-01' AND region = 'North';
b. Top Data Users by Region
To find the top data users in a specific region:
SELECT customer_id, SUM(data_used) AS total_data
FROM data_usage
WHERE region = 'North'
GROUP BY customer_id
ORDER BY total_data DESC;
c. SMS Usage Trends by Region
To analyze SMS usage trends in a specific region:
SELECT customer_id, SUM(sms_count) AS total_sms
FROM sms_data
WHERE region = 'North'
GROUP BY customer_id;
6. Advantages of Partitioning and Bucketing
- Partitioning: Optimizes data retrieval for time-based and regional queries, reducing scan time for large datasets.
- Bucketing: Improves query performance for specific customer-centric queries, helping analyze data for individual customers in telecom operations.
Conclusion
Conclusion
Partitioning and bucketing in Hive for telecommunication data improves query performance by optimizing data storage and retrieval. Partitioning by call_date, usage_date, and sms_date with region helps in faster regional and time-based queries, while bucketing by customer_id speeds up customer-centric queries, making it easier to identify trends and heavy data users.
```
