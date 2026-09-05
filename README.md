## Serverless CRUD API with AWS Lambda, API  Gateway  & DynamoDB

A production-ready guide to deploying, testing, and optimize a decoupled serverless REST API backend on AWS. This project implements secure multi-action request routing    using a single Python runtime handler and optimize the resource profile using data-driven load testing.

## Architecture

<img width="905" height="420" alt="GIT_Serverless_CRUD_API (2)" src="https://github.com/user-attachments/assets/5814378f-a973-48a8-8d0b-7ccfe8465a07" />

1. AWS IAM Role/Policies: Lambda to connect DynamoDB AWS service and perform CRUD operations.
2. Amazon API Gateway: Directs HTTP payload endpoints securely via a custom resource /dynamodbmanager.
3. AWS Lambda Execution Engine: Runs an optimized Python 3.13 handler processing multiple operations.
4. Amazon DynamoDB: Stores non-relational table objects securely via partition key indexes.

## Scope of API
The API accepts a POST request with a JSON payload specifying an operation, a tableName, and a payload.
Supported operations:

create – add a new item to the table
read – fetch a single item by key
update – modify an existing item
delete – remove an item
list – scan and return all items in the table
echo / ping – simple test operations, not tied to DynamoDB

## Step-by-Step Deployment Guide

Step 1: Enforce Least-Privilege IAM Policies
1. Open the Policies page in the IAM Console and click Create Policy.
2. Select the JSON editor and paste the configuration located in iam-policy.json. This scopes Lambda capabilities down exclusively to required DynamoDB table mechanics and CloudWatch logging permissions.
<img width="1268" height="712" alt="image" src="https://github.com/user-attachments/assets/5d9874af-9b4d-4442-8cad-f0ef03e00a34" />

3. Name the policy lambda-custom-policy.
4. Go to Roles > Create Role. Choose AWS Service and select Lambda as the use case. Attach lambda-custom-policy and name the role lambda-apigateway-role.

Step 2: Implement the Lambda Router
1. Provision an AWS Lambda function from scratch named LambdaFunctionOverHttps using Python 3.13.

<img width="1305" height="672" alt="image" src="https://github.com/user-attachments/assets/e42f3816-1f04-4910-bbc0-007c87d71661" />
2. Under Permissions, change the default execution role to Use an existing role and select lambda-apigateway-role.
3. Swap out the boilerplate execution code with the routing block located in lambda_function.py and click Deploy.

<img width="1522" height="441" alt="image" src="https://github.com/user-attachments/assets/22784cd1-f06b-4711-8c72-6dc683bd0504" />
4.Run an execution test named echotest inside the console with an echo operation payload to confirm the runtime logic is sound.

Step 3: Provision the DynamoDB Table
1. Open the DynamoDB Console and click Create Table.
2. Deploy a table named exactly lambda-apigateway.
 <img width="1492" height="422" alt="image" src="https://github.com/user-attachments/assets/1a669956-3ed2-4417-82d0-311016d8a20b" />
3. Test the lambda function to make sure the response is as expected.
 <img width="1157" height="602" alt="image" src="https://github.com/user-attachments/assets/4356b30a-edfd-45d4-bdc9-cac6f03b3255" />

 Step 4: Configure API Gateway Routing
1. Inside the API Gateway Console, create a new REST API named DynamoDBOperations.
2. Click Create Resource and enter DynamoDBManager (Path: /dynamodbmanager).
<img width="1035" height="281" alt="image" src="https://github.com/user-attachments/assets/fdd48950-df2f-41e0-b716-11e0dfb92186" />
3. Select your new resource, click Create Method, choose POST, and select LambdaFunctionOverHttps as the integration backend function.
4. Click Deploy API, select [New Stage], name it Prod, and copy your active Invoke URL.

  

Step 5: Functional Verification
1. Open Postman and pass a structured JSON payload over HTTPS to your live endpoint to trigger a database write operation.
 <img width="892" height="771" alt="image" src="https://github.com/user-attachments/assets/9f3fc053-027d-4acc-ab3a-90111a750b08" />
2. Head to the DynamoDB Console under Explore table items to visually validate that the record successfully wrote to the NoSQL database.
<img width="1232" height="760" alt="image" src="https://github.com/user-attachments/assets/6700bcd2-a8eb-499e-bf86-902371527fe6" />

Phase 5: Optimization & Microservice Load Testing
This module addresses the Performance Efficiency and Cost Optimization pillars of the AWS Well-Architected Framework.

1. AWS Lambda Power Tuning Configuration
Using the AWS Step Functions execution framework (aws-lambda-power-tuning), the system was systematically profiled using 10 concurrent requests across variable resource boundaries: 128MB, 256MB, 512MB, and 1024MB.

Input benchmarks can be reproduced utilizing the template provided inside /test-payloads/lambda-power-tuning-input.json.

Performance Optimization Walkthrough:
Step A: Deploy the aws-lambda-power-tuning nested application from the Serverless Application Repository.
<img width="1714" height="781" alt="image" src="https://github.com/user-attachments/assets/3a0893d0-259b-4724-85b8-16a93e45fb3b" />

Step B: Configure the input JSON tracking parameters inside the Step Functions dashboard execution screen.
<img width="1707" height="869" alt="image" src="https://github.com/user-attachments/assets/a3aac348-bbc0-4761-9777-e2d4a2fca708" />

Step C: Verify successful state machine branch execution runs.
<img width="1574" height="774" alt="image" src="https://github.com/user-attachments/assets/da580ff9-963f-4fda-ba3c-5a8004c9a89d" />
Step D: Extract the unique visualization analytics URL link from the output execution log block.
<img width="1075" height="744" alt="image" src="https://github.com/user-attachments/assets/7cdfdabd-d28d-4dc4-b151-8438170d8730" />

The resulting cost-performance trade-off visualization chart revealed the optimal resource inflection points:












