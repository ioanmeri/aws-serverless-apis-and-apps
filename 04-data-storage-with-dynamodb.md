# Section 4: Data Storage with DynamoDB

## What is DynamoDB

- A NoSQL Database
- No Relations
- Managed by AWS

**Data Format**
- Key, Value

---

## How DynamoDB Organizes Data

Always required: **Partition Key**, must be unique

| Partition Key | Attribute |
| -- | -- |
| UserID | FirstName|

e.g. Data stored in different SSDs based on partition key 

Optional: Partition Key + Sort Key

| Partition Key | Sort Key | Attribute
| -- | -- | -- |
| UserID | Timestamp | HeartRate |

Can also set a **Global Secondary Index** and **Local Secondary Index**

---

## NoSQL vs SQL

NoSQL
- No Relations
- High Flexibilility (No/Weak Scheams)
- Data Repetition
- No Integrity Checks
- Easy Scalability

SQL (Relational)
- Relations
- Limited Flexibility (Strong Schemas)
- No Data Repetition
- Integrity Checks
- Harder Scalability

---

## AWS Dynamo DB + AWS Lambda

Event Source -> Trigger Function -> Lamda Function

Data Repository <- Store & Retrieve Data <- Lamda Function

---

## Creating a Table in Dynamo DB

Services > Amazon DynamoDB > Create table > table name > Partition key (e.g. UserId)

Can also add sort key and index 

---
