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
