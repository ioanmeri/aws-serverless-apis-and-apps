# Creating an API with API Gateway & AWS Lamda

## API Gateway

Create a RESTful API with ease

### How it works

**Application**: e.g. Web App / Mobile App / Other Source (Postman)

sends request

**REST API Gateway**

- Define API Endpoints & HTTP Methods (e.g. POST /book)

- Authorize Access

**Action**

- Directly access some AWS Services

- Run Lamda Code (and forward Request Data)

---

## General API Gateway Features

**Usage Plans**

Restrict the usage of the API by certain keys (e.g. 1000 times per second)

**API Keys**

Useful when **sharing an API with other developers**, not with users of your app

e.g. Google Maps API Key send with any request you sent

**Custom Domain Names**

Allows you to connect your own domain which you own to your API

**Client Certificate**

Important if you plan to forward incoming requests to yet another HTTP endpoint, and on that other HTTP endpoint you want to validate that the request this endpoint gets stems from your API Gateway API.

With client certificate, you can generate a certificate which proves this to your final API

**Settings**

Manage the permissions this service has. By default, it's AWS service has no permissions at all.

If you want to generate logs for API Gateway for example, you would have to give it the right permissions.

---

## API-specific Features & Options

**Resources**

Manage resources and methods from our API.

A resource is just like a path.

To make it live you always have to click on Actions > Deploy API

**Stages**

Stages are like snapshots of your API, deployed snapshots. You need to deploy the different stages.

The actual work happens under resources.

**Authorizers**

Allows you to add authentication to your API

**Models**

Allows you to define the shape of the data you work with in the API (optional)

You can you the models to validate incoming data, see if it fits that schema and reject it if it does not.

**Documentation**

Important if you plan to expose your API to other developers.

**Binary Support**

Important if you plan to sending files along with your requests.

**Dashboard**

High level how API performs and works

---

## Introducing the Request-Response Cycle

Request = Path + Method

Request-Response Cycle depicts the flow of the data

can also Test an API from the console

### 1. Hits the **Method Request** box

Defines how requests reaching this endpoint should actually look like.

We can reject requests if they don't fit a certain schema

In Settings: can set up

- Authorization
- Request Validator
- API Key Required

Can check for **URL Query String Parameters**

Check if **HTTP Request Headers** fit our scheme or needs

Can also check the **Request Body** and can connect it to a **model**

acts like a gatekeeper, it ensures that incoming requests have a certain shape, have certain data, fulfill certain requirements.

### 2. Integration Request

It's about transforming incoming data into the shape we want to use it on the action we'are about to trigger.

The role is to trigger this mock / lamda or something else and possibly if we want to transform our incoming request data.

Not only body data, also headers or metadata like authenticated users.

### 3. Integration Response

The first thing which gets triggered as soon as our action is done.

The same with what Integration Request does, just the other way around.

### 4. Method Response

Gatekeeper for response data. Defines the shape that our response should have.

- Configure different status codes
- What headers should allow to have
- Type of data
- Can use models

It's not binding because we need to send a response at the end.

---

## Creating a Resource (= URL Path)

Configure as **proxy resource** means it will catch-all resource, catching all other paths and methods.

It can be used e.g. for a fullstack express server (limited support)

---

## Handling CORS and the OPTIONS Preflight Request

CORS = Cross Origin Resource Sharing

It's a security model where in general it's not allowed to access resources on a server from another server.

The server e.g. **api.com** need to send the right headers to the client e.g. **example.com** to allow cross server communication

**Preflight**

It's send to anything but GET and HEAD requests. Are there to check if the post request that's about to be sent is available and if it's allowed to send a POST request.

So you need to provide an options endpoint so that Chrome can send its preflight request and this OPTIONS endpoint also need to return the right CORS headers to inform Chrome that it is OK to send a POST, and it is ok to do so even though we're using different resources, different servers.

In AWS the checkbox

**Enable API Gateway CORS**

is automatically configured to send the right headers back to the client / browser

Now in the Request Lifecycle of OPTIONS request in the **Integration Response** > **Header Mappings** we see the headers related to CORS:

- Access-Control-Allow-Headers
- Access-Control-Allow-Methods
- Access-Control-Allow-Origin

---

## What is AWS Lamda?

How it works:

**Event Source**

- **S3(e.g. file gets uploaded)**
- **CloudWatch(scheduled)**
- **API Gateway (HTTP Request)**
- ...

triggers

v

**Your Code**

- NodeJS / Python / Java / C#
- ...

v

**Result**

- Interact with other AWS Services

v

**Return Response**

### AWS API Gateway + AWS Lamda

- POST /book ---> Lamda Function
- GET /books ---> Lamda Function
- DELETE /book/:id ---> Lamda Function

---

## Create a Lamda

**Author from scratch** > enter name / select programming language > Create function

On the left Side are triggers, on the right side access rights your function has (e.g. Amazon CloudWatch Logs)

Write the node.js function in the editor

```
exports.handler = (event, context, callback) => {
  callback(null, {message: 'Hi, I'm Lamda!});
}
```

can pass

- Environment variables
- Tags (for analytics or builts)
- Execution role (defines the permissions of this function)
- Basic settings (timeout to avoid infinite loop charging)

---

## Body Mapping template

**They simply allow you to control which data your action receives (e.g. Lambda)**

**Transform API Gateway data to proper structure for Lamda function**

In the integration request and response we can use it to map the data passed to the action

Integration request > Body Mapping Templates > add template (e.g. application/json)

`$input`  is a reserved variable, provided by AWS which gives you access to the input payload (request body, params, headers) of your request.

Template mapping example:
```
{
  "age": $input.json('$.personData.age')
}

```

$input = Variable provided by AWS, gives you access to your request data (body, params, ...)

json('$') => Extracts complete request body

With body mapping template we have a clear separation between API Gateway where we receive our request and work with it and lambda where we expect to get data in a certain structure and then do something with it.

In integration response we can also use mappings.

### Mapping Response Data

`$input` is the callback info sent back by Lambda, `json('$')` then gives us the data passed with the callback

---

## Using Models & Validating Requests

To create a Model:

1. API Resource
2. Go to API Resouce -> Models
3. Create Data Model in **JSON schema** format
4. Go to API Resource -> Resources
5. Click on **Method Request**
6. In Request Body register the Model

---

## Models & Mappings

Models provide an easier way to provide validation but the same can be done in lambda function.

We can use models in Integration Request (which is for transforming data and forwarding it in the action)

---

## Path parameters

API resource > New Child Resource > Resource Path

```
/compare-yourself/{type}
```

Now a GET method in the path can be created.

In Integration request add a Body Mapping Template with content-type: `application/json`

```
{
  "type": "$input.params('type')"
}
```

In lambda:

```
const type = event.type;
```

---


