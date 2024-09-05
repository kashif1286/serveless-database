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
