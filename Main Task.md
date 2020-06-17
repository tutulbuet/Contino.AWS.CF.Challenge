# API Gateway Challenge

## Background
A cloud engineer working for Company X created a Cloudformation template to deploy a serverless API
service which will receive and store data about sports teams into a NoSQL database.

After deploying the stack, although the AWS Cloudformation stack is successfully created, when the API
receives a POST request the engineer is facing some issues and data is not being written to the table as
expected.

The Cloudformation stack mainly deploys the following AWS resources:

|Service        |Description|
|---------------|-----------|
|API Gateway    |Handles the http traffic|
|Role           |Role to be used by Lambda Function|
|Lambda Function|Extract data from the http request and update the database|
|DynamoDB Table |NoSQL Database|

## Tasks
Based on this scenario, please work on below tasks:

**Main Task**

- Make the necessary changes so data is successfully written to the DynamoDB Table once a
POST request is received by the API Gateway.



***********************************************************
*
* Bonium's Response Below
*
************************************************************



- Share your code with us through your own git repository. Remember to make your repository
private so only we can review it.

Response:
Code uploaded. Repository is private to my own repository. Only shared to ContinoTest.

How to run the code and test:

Deployment:
If you (ContinoTest) have CI (e.g. Jenkins or Teamcity) hooked up with github which is hooked up with AWS, you can run the code directly from the CI.

However, assuming you don't have the CI implemented, all you need is to go to aws console > services > cloudformation > Create Stack > upload the template or use the designer lab to copy and paste the code in the yaml section and upload it after validation. In the parameter it will ask for "MyName" and the name of the stack. After that you can check the box to allow the IAM role creation by the template. Then keep hitting next until the stack creation starts.

Test:
go to aws console > services > API Gateway > API > select the correct API>POST > it should show the stack and give option for client testing. In the testing body you can enter any dictionary as below keeping the partition intact (which is 'team')

 {
 "team_name": "Barcelona",
 "team_could": "Spain",
 "team_desc": "La Liga",
 "team_rating": "1"
 }
 
and hit the test buttong - volla....!!!! you should see the output in the log and also check the DynamoDB table. You should see the item just tested in the POST.

Code Review:
The mentioned cloud engineer's code couldn't write to dynamodb because of 2 reasons:
a). LambdaExecutionRole didn't have any access defined for the dynamodb service. I have refactored the code by adding a policy to provide PutItem access to DynamoDB. There was nothing mentioned about batchwrite or updating the table. So kept the access to PutItem only. 
b). In the original challenge, the Cloud engineer's boto3 resource table was defined incorrectly. It was provided a static value db_name= "Challenge_DB" in the lambda function, however, the Cloudformation TableName was dynamically declared as 
TableName: !Sub Challenge_${MyName}. So the actual table would have the name ending with the environment variable at the end. As the table name was not matching, it was not able to write items.


- Consider security on all pieces of your work.
Response:
Removed the root access for the lambda execution role by providing access to basic execution such as cloud logs. For DynamoDB, only provided access to PutItem and also specified the ARNs for all execution roles to make the security tighter.

- Make sure that all resources required by your stack are included on the deployment process.
Response:
- Included.

- On the same README file, include a section with recommended improvements to be done on
the stack/repo that you consider important.
Response:
- It would be good to have the dynamodb table created with dynamic values and the table name in the lambda function for putitem can be inserted dynamically. This will allow this template the ability to scale. 
- Would be good to have a function to check if the table exist and then putitem.
- Security can be better by adding authentication to the API
- Serverless (sls) tool can be used to write this template even easier and smarter way as SLS will take care of the framework.



