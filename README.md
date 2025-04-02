# AWS-ProjectsE2E
Project-1 Lambda API Gateway and DynamoDB
 Test API Gateway and Lambda Integration
 curl -X POST "https://your-api-id.execute-api.us-east-1.amazonaws.com/dev/resource" \
     -H "Content-Type: application/json" \
     -d '{"key": "value"}'

 Store and Retrieve Data from DynamoDB
 import json
import boto3
import os

dynamodb = boto3.resource('dynamodb')
table_name = os.environ['DYNAMO_TABLE']
table = dynamodb.Table(table_name)

def lambda_handler(event, context):
    body = json.loads(event['body'])
    
    table.put_item(Item={
        'id': body['id'],
        'name': body['name'],
        'email': body['email']
    })

    return {
        'statusCode': 200,
        'body': json.dumps({'message': 'Data stored successfully!'})
    }

Lambda Code for Retrieving Data (GET Request)
def lambda_handler(event, context):
    user_id = event['queryStringParameters']['id']
    
    response = table.get_item(Key={'id': user_id})
    
    return {
        'statusCode': 200,
        'body': json.dumps(response.get('Item', {}))
    }

Terraform Configuration for Lambda + API Gateway
resource "aws_lambda_function" "my_lambda" {
  function_name = "myLambdaFunction"
  handler      = "lambda_function.lambda_handler"
  runtime      = "python3.8"
  role         = aws_iam_role.lambda_role.arn
  filename     = "lambda_function.zip"
  environment {
    variables = {
      DYNAMO_TABLE = "my-dynamo-table"
    }
  }
}

resource "aws_api_gateway_rest_api" "my_api" {
  name = "serverless-api"
}

