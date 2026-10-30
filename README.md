# AWS  Amazon RDS Deployment in a Custom VPC

[![AWS](https://img.shields.io/badge/Cloud-AWS-orange?logo=amazon-aws)](https://aws.amazon.com/)
[![Database](https://img.shields.io/badge/Database-Amazon%20RDS-blue)](https://aws.amazon.com/rds/)
[![SQL Server](https://img.shields.io/badge/Engine-Microsoft%20SQL%20Server-red?logo=microsoft-sql-server)](https://aws.amazon.com/rds/sqlserver/)

This repository documents a hands-on project where I provisioned a Microsoft SQL Server instance on Amazon RDS within a preconfigured AWS VPC environment.

## Objective
Deploy and validate a Microsoft SQL Server database using Amazon RDS inside a pre-provisioned VPC.  
The lab focuses on networking, security, high availability, and validation using SQL Server Management Studio (SSMS).

## Architecture Overview

**AWS Components Used**
- **VPC (Lab-VPC):** Existing VPC with subnets, route tables, and Internet Gateway.
- **DB Subnet Group (RDS-SubnetGroup):** Two subnets across different Availability Zones.
- **Security Group (RDS-SecurityGroup):** Allows inbound SQL Server traffic on port 1433.
- **Amazon RDS:** Microsoft SQL Server Express instance (Free Tier).

<figure>
  <img src="https://github.com/JuanH-Dv/aws-rds-lab/blob/main/screenshots/LabRDSFinalArch.png?raw=true">
  <figcaption style="text-align: center;">Application Architecture </figcaption>
</figure>

## Bussiness Use Case

A **retail startup** needs a managed relational database to store:
- Customer information
- Orders and transactions
- Inventory data

Instead of managing SQL Server on EC2, the company uses **Amazon RDS** to benefit from:
- Automated backups
- Managed patching
- Multi-AZ resilience
- Easy scaling

This project simulates the **initial database setup** for such an environment.

## Implementation Summary

1. Created a **Security Group** allowing inbound TCP traffic on port 1433.

<figure>
  <img src="https://github.com/JuanH-Dv/aws-rds-lab/blob/main/screenshots/RDS_Security_Group.png?raw=true">
  <figcaption style="text-align: center;">RDS Security Group</figcaption>
</figure>

2. Created an **RDS Subnet Group** with two subnets in separate AZs.
3. Launched an **Amazon RDS SQL Server Express instance**:
   - Instance class: `db.t3.micro`
   - Public access: Enabled
   - Attached Security Group and Subnet Group

<figure>
  <img src="https://github.com/JuanH-Dv/aws-rds-lab/blob/main/screenshots/RDS_Instance.png?raw=true">
  <figcaption style="text-align: center;">RDS Instance Creation</figcaption>
</figure>

## Validation & Testing with SSMS

### Step 1: Retrieve the RDS Endpoint
- AWS Console → RDS → Databases → lab-database
- Copy the endpoint and confirm port `1433`

### Step 2: Connect Using SSMS
- Server name: `<endpoint>,1433`
- Authentication: SQL Server Authentication
- Username: `admin`
- Password: `#LabDBase3!`

### Step 3: Create a test Database
Once connected, I opened a new query window in SSMS and executed:
```sql
CREATE DATABASE LabTestDB;
GO

USE LabTestDB;
GO
```

### 4. Create a Table

To model a simple customer entity, I created the `Customers` table:

```sql
-- Create a table for customers
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    Name NVARCHAR(100),
    Email NVARCHAR(100)
);
GO
```

The table definition confirms the ability to create schemas with primary keys and Unicode-capable text fields on the managed SQL Server engine.

<figure>
  <img src="https://github.com/JuanH-Dv/aws-rds-lab/blob/main/screenshots/SSMS_Table_Creation_.png?raw=true">
  <figcaption style="text-align: center;">DB Table Creation</figcaption>
</figure>

### 5. Insert Test Data

I inserted sample customer records to validate write operations:

```sql
INSERT INTO Customers (CustomerID, Name, Email)
VALUES (1, 'Alice Johnson', 'alice@example.com'),
       (2, 'Carlos Ramirez', 'carlos@example.com'),
       (3, 'Juan Hernández', 'juan@example.com');
GO
```

Successful insertion demonstrated proper transaction handling and basic data persistence on the RDS instance.

### 6. Run Queries

I then ran several queries to validate read and update behavior:

```sql
-- Retrieve all rows
SELECT * FROM Customers;
GO

-- Filter example
SELECT Name, Email 
FROM Customers
WHERE CustomerID = 2;
GO

-- Update a record
UPDATE Customers
SET Email = 'c.ramirez@example.com'
WHERE CustomerID = 2;
```

<figure>
  <img src="https://github.com/JuanH-Dv/aws-rds-lab/blob/main/screenshots/SSMS_Query_3.png?raw=true">
  <figcaption style="text-align: center;">Query to update a db record</figcaption>
</figure>

These queries verified that:

- The table returned all inserted records.
- Filtering by `CustomerID` worked as expected.
- Update operations correctly modified the targeted row, confirming end-to-end CRUD capabilities against the managed database.

## Key Learnings

- Gained practical experience configuring **Amazon RDS for SQL Server** within an existing VPC, including subnet groups and security groups.
- Practiced balancing **accessibility vs. security** by exposing the database publicly for learning purposes while understanding network and firewall implications.

---

**Note**: While the project environment allowed public access for testing, a production architecture strictly enforces **private connectivity**, **encryption**, and **managed secrets** to ensure the retail customer and transaction data remains secure.


