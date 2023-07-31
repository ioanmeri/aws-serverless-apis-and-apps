## What is Serverless Development

### Traditional Web Hosting - Apps

**User > Web App / Mobile App**

Sends and receives Data (no HTML)

**Backend**
(REST API)

Access Database
Business Logic

--> Server (Runs PHP/Node/...)

--> Server (Runs PHP/Node/...)

--> Server (Runs PHP/Node/...)

As our application grows and scales up, we need more and more servers to handle the incoming traffic. This can also be handled on our own data center or using a cloud provider like AWS.

**Issue**: Re-invent the Wheel

**Issue**: Servers are online even if not required

**Issue**: Over-oder underprovision

**Isuue**: Keep OS & software updated

Serverless development is best if you have a decoupled frontend and backend, where the backend is such an API and the front end is an application either running on a mobile device or a web application driven by JS, a SPA.

---

## Serverless Apps

Servers still involved but we don't manage them at all.

Instead, we use a special AWS service, it's called Lamda, which allows us to host our code, which is only executed on demand, when it needs to run.

That means we don't need to care about how many servers we will need.

We don't have to pay any idle time.

We may use Node.js / Java / Python / C# when using Lamda service.

---

### Traditional vs Serverless

**Traditional Web Hosting**

- Provision Capacity

  - How much Server Capacity? CPU, RAM, Memory
  - Scaling (Pay too much or Server Issues)

- Update OS & Software

  - Prevent Security Issues by thoughtfully updating software

- Lots of Overhead for SPA + API Apps / Great Support for Fullstack Apps

**Serverless** Web Hosting

- Unlimited Capacity, Only Pay for Code Executions

- Scales Automatically (Pay what you Need)

- Runs on Managed AWS infrastructure

- Code runs in up-to-date and secure Environment

- Great for SPA + API Apps / Limited Support for Fullstack Apps

---

## Why AWS?

there are others e.g. Microsoft Azure, Google Cloud Platform

**AWS**

- Market Leader

- Aggressive Pricing

- Most Serverless Services

- Rapid Innovation & New Features

---
