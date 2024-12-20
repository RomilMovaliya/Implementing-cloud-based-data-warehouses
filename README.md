# Aim: Implement cloud based data warehouses like Amazon Redshift to store and analyse large volumes of structured and unstructured data.

# Step 1: Set Up Amazon S3 to Store Data

## 1. Create an S3 Bucket:
-	Log in to the AWS Management Console.
-	Navigate to the S3 service and create a bucket with a unique name (e.g., my- data-bucket).
-	Select a region near your Redshift cluster for low latency.
-	Configure public access settings as per your security requirements and enable versioning if needed.

  
![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(670).png)

## 2.	Upload Data to S3:
-	Prepare your structured and unstructured data.
-	Use the AWS Console, AWS CLI, or SDK to upload the data into your S3 bucket.

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(669).png)

# Step 2: Set Up an IAM Role for Redshift Access

## 1.	Create an IAM Role:
-	Go to the IAM service and create a new role with the AWS Service: Redshift
use case.
-	Attach the policy AmazonS3ReadOnlyAccess to this role, allowing Redshift to access S3.
-	Ensure the trust relationship is set to allow Redshift to assume this role.

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(671).png)



![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(674).png)





# Step 3: Set Up Amazon Redshift
## 1.	Create a Redshift Cluster:
-	In the Amazon Redshift service of the AWS Management Console, click Create cluster.
-	Choose the node type and number of nodes (e.g., dc2.large, 2 nodes).
-	Specify the database name, master username, and password.
-	Choose the VPC where Redshift will reside. Ensure it is in the same region as your S3 bucket.


![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(675).png)


![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(676).png)

## 2.	Set Cluster Security:
-	Ensure that the Redshift cluster has appropriate security group settings to allow access from your IP range.
## 3.	Attach IAM Role to Redshift Cluster:
- After creating the cluster, go to the Configuration tab of your Redshift cluster.
-	Attach the IAM role you created earlier by selecting it under the IAM roles section.
 


![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(677).png)

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(678).png)

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(679).png)

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(680).png)

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(681).png)

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(682).png)









 



 

 
 

 
 

 
 

 





# Step 4: Set Up SQL Workbench/J
## 4.1	Download and Configure SQL Workbench/J
## 1.	Download SQL Workbench/J:
-	Visit SQL Workbench/J download page and download the latest version for your operating system.
 
## 2.	Configure Connection to Redshift:
-	Open SQL Workbench/J.
-	Create a new connection profile:
  
```
Driver: Select Amazon Redshift.
URL: Set the JDBC URL to your Redshift cluster's endpoint (e.g., jdbc:redshift://your-cluster-name.region.redshift.amazonaws.com:5439/yourdb).
User: Enter the master username.
Password: Enter the master password.
Test Connection to ensure that the settings are correct.
```

 
# 4.2	Install Redshift JDBC Driver
## 1.	Download the Redshift JDBC Driver:
-	Download it from Amazon's official website.
-	In SQL Workbench/J, go to File → Manage Drivers, and add the path to the Redshift JDBC jar file.

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(683).png)

![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(684).png)

 
# Step 5: Convert Raw Data from S3 into Database Schema
## 5.1 Create a Schema in Redshift
## 1.	Create a Schema:
-	Use SQL Workbench/J to connect to Redshift and execute the following SQL commands to create a schema and tables that match your data structure:

```mysql
-- Create a schema called 'customer_data'

  CREATE SCHEMA customer_data;
 

-- Create a table for customer orders

  CREATE TABLE customer_data.orders (
  OrderID INT PRIMARY KEY,
  CustomerID INT,
  OrderDate DATE,
  OrderAmount FLOAT,
  OrderStatus VARCHAR(50)
);

-- Create a table for customer feedback

  CREATE TABLE customer_data.feedback (
  FeedbackID INT PRIMARY KEY,
  CustomerID INT,
  FeedbackData JSON
);
```

-	Load Structured Data (CSV) into Redshift
-	To load the orders.csv file stored in an S3 bucket into your orders table, you will use the COPY command. Here's the SQL syntax:

```mysql

-- Rollback any failed transactions ROLLBACK;

-- Create the schema if it doesn't exist
CREATE SCHEMA IF NOT EXISTS customer_data;


-- Create the table for customer feedback with JSON data type for FeedbackData
-- Load structured data (CSV) into the orders table COPY customer_data.orders
FROM 's3://my-data-bucket-romil/orders.csv'
IAM_ROLE 'arn:aws:iam::021891615597:role/Redshift-role'
 
CSV
DELIMITER ','
IGNOREHEADER 1;

-- Commit the transaction (if applicable) COMMIT;
```


-	Load UnStructured Data (JSON) into Redshift
-	To load the feedback.JSON file stored in an S3 bucket into your orders table, you will use the COPY command. Here's the SQL syntax:

```mysql

-- Rollback any failed transactions
ROLLBACK;

-- Create the schema if it doesn't exist
CREATE SCHEMA IF NOT EXISTS customer_data;


-- Create the table for customer feedback with SUPER data type for FeedbackData
CREATE TABLE customer_data.feedback (
  FeedbackID INT PRIMARY KEY,
  CustomerID INT,
  FeedbackData SUPER -- Use SUPER type for semi-structured data
);


-- Commit the transaction (if applicable)
COMMIT;
```

## Next Steps:
-	After successfully creating the schema and table, you can proceed with the COPY
command to load your data from S3 into the Redshift table.
-	Verify the data with a simple query like:

```mysql 
SELECT * FROM customer_data.feedback LIMIT 10;
```

 
![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(687).png)

 
![Screenshot1.JPG](https://github.com/RomilMovaliya/Implementing-cloud-based-data-warehouses/blob/main/Screenshot%20(688).png)


# AWS Free Tier Limits and Monthly Costs

This document outlines the free tier limits and associated monthly costs for various AWS services.

## Services and Costs

| Service              | Free Tier Limit                                                                                     | Monthly Cost        |
|----------------------|----------------------------------------------------------------------------------------------------|---------------------|
| **Amazon S3**        | - 5 GB storage<br>- 2,000 PUT requests<br>- 20,000 GET requests<br>- 15 GB data transfer out         | $0                  |
| **Amazon Redshift**  | - 1 DC2.large node<br>- 750 hours/month<br>- 2 months free                                          | $0 (for the first 2 months) |
| **IAM Role**         | No cost                                                                                           | $0                  |
| **Data Transfer (Redshift)** | 15 GB data transfer out                                                                      | $0 (if under 15 GB) |

## Notes

- All costs are subject to AWS free tier limits.
- Additional usage exceeding the free tier may incur charges.
- Please refer to the official [AWS Pricing](https://aws.amazon.com/pricing/) page for detailed information.



