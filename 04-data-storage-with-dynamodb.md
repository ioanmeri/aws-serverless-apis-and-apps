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
