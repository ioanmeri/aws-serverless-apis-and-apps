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

## Accessing DynamoDB from Lambda

You don't need to install `aws-sdk` from lambda. Only needed if you plan on using the SDK from you machine.

Why outside of the handler function? The code running here is not online all the time. Whenever a trigger occurs running your lamda function AWS quickly spins up a server environment, wrapper containing your lambda function.
It keeps it alive for a couple of minutes and it will reuse that wrapper if it remains active on requests.

Here we are taking advantage of it, if the wrapper start it will execute everything in this file not just your function handler, if the wrapper is still up though, it will not re-execute the part outside of the function handler and that gives us a little performance edge if we put the code which doesn't need to be executed it on each triggering event outside of the handler.

```
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB({
  region: 'us-east-2',
  apiVersion: '2012-08-10'
})

exports.fn = (event, context, callback) => {
  
}
```

---

## Putting Items into a DynamoDB Table from Lambda

```
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB({
  region: 'us-east-2',
  apiVersion: '2012-08-10'
})

exports.fn = (event, context, callback) => {
  const params = {
    Item: {
      "UserId": {
        S: "dasf87af8safa"
      },
      "Age": {
        N: "28"
      },
      "Height": {
        N: "72"
      },
      "Income": {
        N: "2500"
      }
    },
    TableName: "compare-yourself"
  };
dynamodb.putItem(params, function(err, data){
  if(err){
      console.log(err);
      callback(err);
    } else {
      console.log(data);
      callback(null, data);
    }
  });
}
```

---
