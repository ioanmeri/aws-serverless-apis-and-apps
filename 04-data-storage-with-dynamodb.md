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

## Setting Permissions Right

Service IAM &rarr; Roles &rarr; Find role used in lambda &rarr; Attach Policy &rarr; Search for  "AmazonDynamoDBFullAccess"

---

## Using API Gateway (Request) Data for Item Creation

To forward specific parameters from Integration Request via Template to Dynamo DB

**In Lamda Template**

```
#set($inputRoot = $input.path('$'))
{
  "age": "$inputRoot.age",
  "height": "$inputRoot.height",
  "income": "$inputRoot.income",
}
```

You can also forward the full request body.

The template is used not only to extract data but also to transform the type of data from **number** to **string**.

**In Lamda function**

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
        S: "user_" + Math.random()
      },
      "Age": {
        N: event.age
      },
      "Height": {
        N: event.height
      },
      "Income": {
        N: event.income
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

## Mapping the Response

If there is no Integration Response lamda will forward everything 

**Web test**

```
var xhr = new XMLHttpRequest();
xhr.open('POST', 'https://bq4jksowdd.execute-api.us-east-2.amazonaws.com/dev/compare-yourself');
xhr.onreadystatechange = function(event){
  console.log(event.target.response);
}
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.send(JSON.stringify({age: 26, height: 71, income 2100}));
```

---

## Scanning Data in DynamoDB from Lamda

```
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB({region: 'us-east-2', apiVersion: '2012-08-10'});


exports.handler = (event, context, callback) => {
  const type = event.type;
  if(type == 'all'){
    const params = {
      TableName: 'compare-yourself'
    };
    dynamodb.scan(params, function(err, data){
      if(err){
        console.log(err);
        callback(err);
      } else {
        console.log(data);
        callback(null, data);
      }
    });
  } else if (type == 'single'){
    callback(null, 'The single user data is here!');
  } else {
    callback(null, 'Hello from Lamda');
  }
}
```

---

## Improving the IAM Permissions

Full access role it's not a good practice

### Put Item Role

IAM Permissions -> Policies -> Create Policy -> Policy Generator -> AWS Service: Amazon DynamoDB 
-> Actions: PutItem -> Amazon Resource Name (ARN): find ARN from dynamodb table details -> Add Statement -> Policy Name: `dyanomodb-putitem` -> Create Policy

In Roles-> `cy-store-data-role`

Detach Policy of `AmazonDynamoDBFullAccess` and attach the new policy, Filter: Customer Managed and select the `dynamodb-putitem`

The getItem API now is should fail.

### Get Data Role

**For the get item we should create a new policy and a new role for that policy**

IAM -> Policies -> Policy Generator -> AWS Service: Amazon DynamoDB -> Actions: GetItem -> 
Scan -> use the dynaodb ARN -> Add Statement -> Policy Name: `dynamodb-get-scan` -> Create Policy

In Roles ->  Create role -> AWS Lamda -> Policy Type: Customer Managed ->
`dynamodb-get-scan` -> Role name: `cy-get-data-role`

In Lamda -> cy-get-data -> Configuration -> Existing role: `cy-get-data-role` -> Save

Now I have clear permissions settings and no overlappings and no function is allowed to do more than it needs to do, principle of least privilege.


---

## Restructuring Fetched Data in Lamnda

```
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB({region: 'us-east-2', apiVersion: '2012-08-10'});


exports.handler = (event, context, callback) => {
  const type = event.type;
  if(type == 'all'){
    const params = {
      TableName: 'compare-yourself'
    };
    dynamodb.scan(params, function(err, data){
      if(err){
        console.log(err);
        callback(err);
      } else {
        console.log(data);
        const items = data.Items.map(
          (dataField) => {
            return {
              age: +dataField.Age.N,
              height: +dataField.Height.N,
              income: +dataField.Income.N
            }
          }
        );
        callback(null, items);
      }
    });
  } else if (type == 'single'){
    callback(null, 'The single user data is here!');
  } else {
    callback(null, 'Hello from Lamda');
  }
}
```

## Getting a Single Item from DynamoDB via Lambda


```
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB({region: 'us-east-2', apiVersion: '2012-08-10'});


exports.handler = (event, context, callback) => {
  const type = event.type;
  if(type == 'all'){
    const params = {
      TableName: 'compare-yourself'
    };
    dynamodb.scan(params, function(err, data){
      if(err){
        console.log(err);
        callback(err);
      } else {
        console.log(data);
        const items = data.Items.map(
          (dataField) => {
            return {
              age: +dataField.Age.N,
              height: +dataField.Height.N,
              income: +dataField.Income.N
            }
          }
        );
        callback(null, items);
      }
    });
  } else if (type == 'single'){
    const params = {
      Key: {
        "UserId": {
          S: "ha830klajsdf30"
        }
      },
      TableName: "compare-yourself"
    };
    dynamodb.getItem(params, function(err, data){
      if(err){
        console.log(err);
        callback(err);
      } else {
        console.log(data);
        callback(null, {
          age: +data.Item.Age.N,
          height: +data.Item.Height.N,
          income: +data.Item.Income.N,
        }); 
      }
    })
  } else {
    callback('Something went wrong');
  }
}

```

---

## Preparing Delete Permissions


1. IAM -> Policies -> Create Policy -> Policy Generator -> AWS Service: Amazon DynamoDB
-> Actions: DeleteItem

Copy ARN of DynamoDB table from Overview Tab

In Policy

-> Amazon Resource Name (ARN): Paste copied ARN

Policy Name: dynamodb-deleteitem and Create Policy

2. Also Create Role, Filter: Customer Managed and choose: dynamodb-deleteItem
give Role name: cy-delete-data-role

and click create role

3. In Lamda -> Functions -> cy-delete-data -> Existing role: cy-delete-data-role

---

## Giving Lambda Logging Rights

If you do want your Lambda functions to create logs (in CloudWatch), make sure to adjust the "Roles" we created.

For that, visit IAM => Roles and select the roles you created for Lambda. Make sure to add a new policy by clicking "Attach Policy". Then, filter for "Lambda" and choose the "AWSLambdaBasicExecutionRole" which will give Lambda the base permissions it requires for writing logs to CloudWatch.

---
