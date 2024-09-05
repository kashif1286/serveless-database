Level up For Serverless-DB

Let's start with the High Level Design

![high-level-design](https://github.com/user-attachments/assets/705de2e9-7963-45a7-a96c-0497460563f5)

The following is a sample request payload for a DynamoDB create item operation:

    {
        "operation": "create",
        "tableName": "lambda-apigateway",
        "payload": {
            "Item": {
                "id": "1",
                "name": "Bob"
            }
       }
    }

SETUP

1.Create Lambda IAM Role
Create the execution role that gives your function permission to access AWS resources.

To create an execution role

Open the roles page in the IAM console.
Choose Create role.
Create a role with the following properties.
       Trusted entity – Lambda.
       Role name – lambda-apigateway-role.
       Permissions – Custom policy with permission to DynamoDB and CloudWatch Logs. This custom policy has the permissions that the function needs to write data to DynamoDB and upload logs.

    {
       "Version": "2012-10-17",
       "Statement": [
       {
         "Sid": "Stmt1428341300017",
         "Action": [
           "dynamodb:DeleteItem",
           "dynamodb:GetItem",
           "dynamodb:PutItem",
           "dynamodb:Query",
           "dynamodb:Scan",
           "dynamodb:UpdateItem"
        ],
        "Effect": "Allow",
        "Resource": "*"
      },
      {
         "Sid": "",
         "Resource": "*",
         "Action": [
          "logs:CreateLogGroup",
          "logs:CreateLogStream",
          "logs:PutLogEvents"
       ],
       "Effect": "Allow"
    }
    ]
    }

2.Create Lambda Function

![lambda-basic-info](https://github.com/user-attachments/assets/d40230be-fb8e-43ed-8890-7f0634b5ab1e)

PEST THE CODE OF PYTHON

         from __future__ import print_function

         import boto3
         import json

         print('Loading function')


         def lambda_handler(event, context):
              '''Provide an event that contains the following keys:

                 - operation: one of the operations in the operations dict below
                 - tableName: required for operations that interact with DynamoDB
                 - payload: a parameter to pass to the operation being performed
              '''
              #print("Received event: " + json.dumps(event, indent=2))

              operation = event['operation']

              if 'tableName' in event:
                  dynamo = boto3.resource('dynamodb').Table(event['tableName'])

              operations = {
                  'create': lambda x: dynamo.put_item(**x),
                  'read': lambda x: dynamo.get_item(**x),
                  'update': lambda x: dynamo.update_item(**x),
                  'delete': lambda x: dynamo.delete_item(**x),
                  'list': lambda x: dynamo.scan(**x),
                  'echo': lambda x: x,
                  'ping': lambda x: 'pong'
             }

             if operation in operations:
                 return operations[operation](event.get('payload'))
            else:
                 raise ValueError('Unrecognized operation "{}"'.format(operation))

![lambda-code-paste](https://github.com/user-attachments/assets/5b64d402-48ad-4f4d-883c-d4ef7a81aee6)

Lambda test fuction
![lambda-test-event-create](https://github.com/user-attachments/assets/a3c46aed-165a-4b9f-9fc5-558b1ede32e7)

          {
             "operation": "echo",
             "payload": {
                "somekey1": "somevalue1",
                "somekey2": "somevalue2"
             }
         }

![save-test-event](https://github.com/user-attachments/assets/f11f1721-74e8-469b-b7e7-5f783bd09738)

click test

![execute-test](https://github.com/user-attachments/assets/4557e8e5-6876-44a5-aec3-79ceafb38f85)


3.CREAT DYNAMODB TABLE
![create-dynamo-table](https://github.com/user-attachments/assets/1b972631-3915-424d-b6e3-2f30cf502bfe)

4.CREAT API(RESTAPI)
![create-api-button](https://github.com/user-attachments/assets/ea3fb7ec-c11c-4cf3-bd76-a8c889a1ec9f)

4.1 BUILD REST API
![build-rest-api](https://github.com/user-attachments/assets/acfb0cee-aa8f-40b1-93e8-3263a2cd389b)
![create-new-api (1) DYANAMOdb](https://github.com/user-attachments/assets/578d7af7-bb15-42be-a003-9c3333d805c8)

4.2 CREAT RESOURSES
![create-api-resource1](https://github.com/user-attachments/assets/b17cd177-a8c3-43e5-8a85-a36a98205a58)
![create-resource-name](https://github.com/user-attachments/assets/9296e814-ec72-4187-9ba6-60550568251e)

4.3 CREAT METHOD
![create-method-1 (1)](https://github.com/user-attachments/assets/e86cce83-95ec-4797-b71d-0e7ba6ef7f10)
![create-method-2 (1)](https://github.com/user-attachments/assets/841b7ff9-6fd2-4542-8d5d-9a3014a5ca75)
![create-lambda-integration](https://github.com/user-attachments/assets/7d0f39b6-8024-4a85-a866-4d9717acd6e2)

4.4 DEPLOY METHOD
![deploy-api-1 (1)](https://github.com/user-attachments/assets/8e8765cb-b16c-4cab-be09-0dc6a3cbb18f)
![deploy-api-2 (1)](https://github.com/user-attachments/assets/d3bf14a1-c74d-449b-a4e9-e2660b6a594a)
![copy-invoke-url](https://github.com/user-attachments/assets/e454240a-06f9-426a-9498-fdf3615d8551)

5. RUNING SOULUTION

5.1 The Lambda function supports using the create operation to create an item in your DynamoDB table. To request this operation, use the following JSON:

        {
             "operation": "create",
             "tableName": "lambda-apigateway",
             "payload": {
                 "Item": {
                     "id": "1234ABCD",
                     "number": 5
                }
           }
      }

5.2 To execute our API from local machine, we are going to use Postman and Curl command. You can choose either method based on your convenience and familiarity.
               To run this from Postman, select "POST" , paste the API invoke url. Then under "Body" select "raw" and paste the above JSON.
               Click "Send". API should execute and return "HTTPStatusCode" 200.
               
![create-from-postman](https://github.com/user-attachments/assets/bd34886c-079a-415d-9fde-2146a8a775f9)
         To run this from terminal using Curl, run the below

        $ curl -X POST -d "{\"operation\":\"create\",\"tableName\":\"lambda-apigateway\",\"payload\":{\"Item\":{\"id\":\"1\",\"name\":\"Bob\"}}}" https://$API.execute-api.$REGION.amazonaws.com/prod/DynamoDBManager

5.3 To validate that the item is indeed inserted into DynamoDB table, go to Dynamo console, select "lambda-apigateway"
table, select "Items" tab, and the newly inserted item should be displayed.

![dynamo-item](https://github.com/user-attachments/assets/4f4e5de2-39fd-41ae-a7bf-b3866a50ddf4)

5.4 To get all the inserted items from the table, we can use the "list" operation of Lambda using the same API. 
Pass the following JSON to the API, and it will return all the items from the Dynamo table


        {
             "operation": "list",
             "tableName": "lambda-apigateway",
             "payload": {
             }
        }

![dynamo-item-list (1)](https://github.com/user-attachments/assets/035f4ca2-3d28-456e-bf3d-20c2c46aee6c)

Congratulation Your Project SuccessfullyRun..............

CLEAN UP ALL SERVICES


         








