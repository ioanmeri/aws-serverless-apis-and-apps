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


### Read & Write Capacity

While creating a DynamoDB table

Depends on units per second and the **size** of the item of reading / writing (KB)


e.g. 5 times / second

---

### Create Item

On items tab create an item, the primary key is mandatory.

You can append a new item with a specified type

```
{
  UserId: 'ha830klajsdf30',
  Age: 28,
  Height: 72,
  Income Number: 2500
}
```

You can also scan or query the table with filters.

---

## What about multiple Databases?

You can have one database per region

---
