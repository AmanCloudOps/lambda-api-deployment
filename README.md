# Deploy a Serverless Function using AWS Lambda
## Goal: 
Deploy a simple serverless function using AWS Lambda, expose it as an HTTP endpoint via API Gateway.

## Prerequisites:
- Install and configure AWS CLI
- Set up AWS IAM roles with Lambda permissions

✅ **Tech Stack**: 
- AWS Lambda
- AWS API Gateway
- Python/Node.js

---

## 🔹 Steps

### 1. **Write a Simple Function (handler.py)**
Create a simple Python file, `handler.py`, that will return a JSON response. Here is an example Python function:


    nano handler.py


    import json

    def lambda_handler(event, context):
    response = {
        "statusCode": 200,
        "body": json.dumps({
            "message": "Hello from Lambda!"
        })
    }
    return response


### 2. Deploy It as an AWS Lambda Function Using the AWS CLI

#### 1. Create a ZIP package:


    zip function.zip handler.py
 
#### 2. Create the Lambda function using the AWS CLI:

    aws lambda create-function \
        --function-name myServerlessFunction \
        --runtime python3.9 \
        --role arn:aws:iam::<AWS_ACCOUNT_ID>:role/<ROLE_NAME> \
        --handler handler.lambda_handler \
        --zip-file fileb://function.zip

 ## Step 3: Configure API Gateway
 ### 1. Create a REST API

    aws apigateway create-rest-api --name "MyServerlessAPI"
       

 ### 2. Create a Resource
    aws apigateway get-resources --rest-api-id <API_ID>      

 ## Step 4: Create a GET Method and Integration
#### 1. Create a GET method:
    aws apigateway put-method --rest-api-id <API_ID> --resource-id <RESOURCE_ID> --http-method GET --authorization-type "NONE"

 #### 2. Link the method to Lambda:
    aws apigateway put-integration --rest-api-id <API_ID> --resource-id <RESOURCE_ID> --http-method GET --type AWS_PROXY --integration-http-method POST --uri arn:aws:apigateway:<REGION>:lambda:path/2015-03-31/functions/arn:aws:lambda:<REGION>:<AWS_ACCOUNT_ID>:function:myServerlessFunction/invocations


#### 3. Deploy the API       
    aws apigateway create-deployment --rest-api-id <API_ID> --stage-name dev

## Step 4: Grant API Gateway Permission to Invoke Lambda
    aws lambda add-permission \
    --function-name myServerlessFunction \
    --statement-id apigateway-invoke \
    --action lambda:InvokeFunction \
    --principal apigateway.amazonaws.com

#### Test it using:
    curl https://<API_ID>.execute-api.<REGION>.amazonaws.com/dev/myresource    
