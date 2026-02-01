---
date: 2026-01-30
---
```bash
aws configure list-profiles
```

```bash
cat ~/.aws/credentials
```


Policies:
- identity based policy
- recourse based policy

---

EC2 & lambda & elastic beanstalk

- EC2 is a virtual server (**VM**) in the cloud.
	- You choose the instance type and OS, install and run software, manage patches and security updates, and handle scaling (manually or with Auto Scaling).
	- three benefit
		- snapshotting
		- decouple
		- higher hardware untilization
- AWS Lambda
	- Lambda is a **serverless** compute service that runs your code in response to events. 
	- You upload a function, and AWS automatically provisions, runs, and scales the execution. You pay only for the compute time used (per request and duration), and you don’t manage servers.
- AWS Elastic Beanstalk (PAAS)
	- Elastic Beanstalk is a managed platform for deploying and running applications.
	- You upload your application code, and Beanstalk automatically handles provisioning and operating the underlying infrastructure (typically EC2, load balancing, and Auto Scaling), plus deployment and monitoring options—so you can focus more on the app than the servers.

AWS Elastic Kubernetes: 
- manager container's instance

### Serverless computing
With serverless, you deploy code as functions and connect them to triggers (HTTP requests, file uploads, schedules, queues). The platform handles servers and scaling.


### AWS Lambda:
AWS Lambda runs your code as functions in response to **events (triggers)** without you managing servers.
- invoke the lambda_function
- can use s3 as trigger

SNS
SQS

### Amazon EventBridge
Amazon EventBridge is a **serverless event bus** that ingests events from multiple sources, filters/transforms them using rules, and routes matching events to one or more targets.


[REST test test...](https://resttesttest.com/)


### API Gateway

1. Client calls your API endpoint
    Example: `GET /hello`
2. API Gateway routes the request to a Lambda integration
    It passes request data (path, query, headers, body) to Lambda as an **event**.
3. Lambda runs your handler
    Your code processes the event and returns a response object.
4. API Gateway sends the response back to the client
    It converts the Lambda output into an HTTP response (status code, headers, body).


Lambda need library 

- implement one handler, decrease/increase 

```python
import requests

api_url = "https://XXX"

response = requests.get(api_url)

response.json()
```