# Data pipeline using S3,RDS,Glue

The goal is to aggregate customer debit card purchases on a daily basis and update
the aggregated data in a MySQL table hosted on Amazon RDS. I used AWS S3 for
data storage, AWS Glue for data processing, and Amazon RDS for data persistence.

## Step 1: Generate data and store in S3 using Hive style partition
1. Generate mock daily transaction data and store it in CSV files.
2. Upload daily transaction CSV files to an AWS S3 bucket using a Hive-style partition.

   <img width="975" height="651" alt="image" src="https://github.com/user-attachments/assets/a82273fb-05ff-4765-9e59-b688b52b5c09" />
   <img width="975" height="491" alt="image" src="https://github.com/user-attachments/assets/43d78b61-0f3b-4206-bffa-101d41db01a7" />


 
 

## Step 2: Set up a MySQL table in Amazon RDS to store aggregated transaction data.
1.	Go to AWS Console → RDS → Create database.
2.	Choose:
o	Engine: MySQL
o	Create username/password.
3.	Once created, note the endpoint (e.g., mydb.xxxxx.us-east-1.rds.amazonaws.com).
4.	Connect via MySQL Workbench and create the table:
CREATE DATABASE transactions_db;
USE transactions_db;

CREATE TABLE aggregated_transactions (
    customer_id INT,
    debit_card_number VARCHAR(20),
    bank_name VARCHAR(50),
    total_amount_spend DECIMAL(10,2),
    PRIMARY KEY (customer_id, debit_card_number)
);
 
<img width="975" height="449" alt="image" src="https://github.com/user-attachments/assets/21321d52-4ca5-49b1-8c6a-76151d07fd36" />

5.	Make sure security group of rds has port 3306 open for inbound requests
   <img width="975" height="248" alt="image" src="https://github.com/user-attachments/assets/33ed500c-1f12-494e-97a0-739f03e9272e" />

 
6.	Also create an endpoint for S3 in RDS VPC

   <img width="975" height="111" alt="image" src="https://github.com/user-attachments/assets/e5fb47b7-7be7-4ee0-828e-0c5cbdf697c7" />

 

## Step 3: Write an AWS Glue job to process daily transactions from S3, aggregate them, and
update the RDS MySQL table
1.	Set up AWS Glue: 
•	First make sure glue has all necessary IAM permissions
 
2.	Go to Glue → Create Crawler (point to S3 bucket).
3.	Let it create a Glue Data Catalog table.

 

 
Data Catalog showing schema from glue crawler
 
## Step 4: Create a Glue Connection to RDS:
1.	Go to Glue → Connections → Add connection.
2.	Type: JDBC → MySQL → Enter RDS endpoint, DB name, username, password.
3.	Store credentials in AWS Secrets Manager for security.

## Step 5: Create a Glue Job to aggregate data from S3 and push to RDS


 

 
 
 


