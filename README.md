
# ServerLess-Database

I am uses 4 service(IAM role, Lambda, Dynamodb, API(REST-API),
Postman application for runing project)

# Lab overview and high desing
let start with project
![high-level-design](https://github.com/user-attachments/assets/781f86f4-aa04-45b3-b2f6-0bf04205e513)


The POST method on the DynamoDBManager resource supports the following DynamoDB operations:

Create, update, and delete an item.
Read an item.
Scan an item.
Other operations (echo, ping), not related to DynamoDB, that you can use for testing.
The request payload you send in the POST request identifies the DynamoDB operation and provides necessary data. For example:

The following is a sample request payload for a DynamoDB create item operation:

```bash
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
```
```bash
{
    "operation": "read",
    "tableName": "lambda-apigateway",
    "payload": {
        "Key": {
            "id": "1"
        }
    }
}
```
Set Up Of Services

# Create Lambda IAM Role

Create the execution role that gives your function permission to access AWS resources.

To create an execution role

1.Open the roles page in the IAM console,

2.Choose Create role.

3.Create a role with the following properties.
   Trusted entity – Lambda.
   Role name – lambda-apigateway-role.
   Permissions – Custom policy with permission to DynamoDB         and CloudWatch Logs. This custom policy has the permissions that the function needs to write data to DynamoDB and upload logs.

```bash
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
```

# Creat a Lambda function

![server-lambada 1](https://github.com/user-attachments/assets/f06a8bf3-f37c-43df-8f8a-20eb37084d5e)

![server-lambda 2](https://github.com/user-attachments/assets/2eb884c6-450d-4db4-b401-9c4d7e3c95d5)

![server-lamda 3](https://github.com/user-attachments/assets/6983aac9-8e8b-4167-adbb-77c9438f3bc9)


![server-lambda 5](https://github.com/user-attachments/assets/6cb49cf1-04d6-4c18-8b93-fe268f71316f)

add a python code...

```bash
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
```

![server-lambda 4](https://github.com/user-attachments/assets/750f86c8-c4e1-4f55-a122-30bbaf728deb)

Paste the following JSON into the event. The field "operation" dictates what the lambda function will perform. In this case, it'd simply return the payload from input event as output. Click "Create" to save

```bash
{
    "operation": "echo",
    "payload": {
        "somekey1": "somevalue1",
        "somekey2": "somevalue2"
    }
}

```
![server-lambda 6](https://github.com/user-attachments/assets/f9fb1033-d030-48f7-b3c4-82540e4a49d7)

![server-lamda 7](https://github.com/user-attachments/assets/d1b790f6-863b-4e7a-9849-ae1a3217d757)

![server-lambda 8](https://github.com/user-attachments/assets/985fb381-71c2-4cc3-a83d-5bbed32a5e62)

# Create DynamoDB Table


![server-dyanamo 2 1](https://github.com/user-attachments/assets/4a136f12-1b18-48dc-bb17-4133c10c1d36)
![server-daynamo 2 2](https://github.com/user-attachments/assets/ce75c60b-cc5c-4997-9d43-58f96eaaa2a1)
![server-daynamo 2 3](https://github.com/user-attachments/assets/a28d7e40-6b5b-449f-bf65-da5a05d51339)

# Create API

![serve-api 3 1](https://github.com/user-attachments/assets/b64401eb-ebdd-40d1-b173-059f92efe93c)
![server-api 3 2](https://github.com/user-attachments/assets/70a428ef-989f-451c-8e23-bbabfaf516ea)
![server-api 3 3](https://github.com/user-attachments/assets/30a2f308-5257-4836-9080-98ee06904a8d)
![server-api 3 4](https://github.com/user-attachments/assets/24b1b0d1-627d-45c9-8e3b-699064151cc5)
![server-api 3 5](https://github.com/user-attachments/assets/e7f9e9aa-a2f1-4d46-8280-82fbf090781a)
![server-api 3 6](https://github.com/user-attachments/assets/c1055f82-a146-4901-be57-80ad810e9a05)
![server-api 3 7](https://github.com/user-attachments/assets/68daa6ef-a8cf-47b1-acc6-1701eae576cc)
![server-api 3 8](https://github.com/user-attachments/assets/4426dbb6-fe2f-468a-81be-f5505b3f8538)
![server-api 3 9](https://github.com/user-attachments/assets/4957596b-abda-47bc-83f5-17475413f704)
![server-api 3 10](https://github.com/user-attachments/assets/b62b0cdb-b3a9-4b8c-92bf-dc81ec0cc1b7)

# We are run project by Postman application

The Lambda function supports using the create operation to create an item in your DynamoDB table. To request this operation, use the following JSON:

```bash
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

```

![server-postman 4 1](https://github.com/user-attachments/assets/c14dd8bf-2f2e-4dfa-8972-f84fa5cbaa4b)


![server-postman4 2 1](https://github.com/user-attachments/assets/35aa80a9-2195-43c5-abbd-3286bf992f15)

To run this from terminal using Curl, run the below

        $ curl -X POST -d "{\"operation\":\"create\",\"tableName\":\"lambda-apigateway\",\"payload\":{\"Item\":{\"id\":\"1\",\"name\":\"Bob\"}}}" https://$API.execute-api.$REGION.amazonaws.com/prod/DynamoDBManager


CHEAK A PROJECT FOR RUNING
![server-cheak-run 5](https://github.com/user-attachments/assets/fc7d6f7b-6065-486f-9dc0-a627ec5e0a47)


# CONGRATULATION YOUR ARE PROJECT RUNING.......................

# Cleanup All Service
you are run project then you step by step delete your Services











