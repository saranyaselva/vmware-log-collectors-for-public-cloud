# vmware-log-collectors-for-aws

## Overview
The project provides an AWS Lambda function for collecting AWS services logs
and sending the logs to vRealize log insight cloud - VRLIC.

## Try it out
To try out the project, you need to do two things:
1. Deploy the code of this project as a Lambda function in your AWS environment.
2. Configure the Lambda function to collect one of the below AWS services.

The following sections describe each of the above two steps in detail.

### Deploy the Lambda function
1. In the AWS Web Console, create a new Lambda function in AWS.
2. Select Node.js 12.X as the runtime. Click on 'Create Function'.
3. Set the 'Handler' field to 'index.handler'. Set the timeout of the Lambda function to 60 seconds.
4. Select 'Code Entry Type' to 'Upload a .zip file'.
5. Upload the zip file from the latest release in the 'Function Package'.
6. Add the below environment variable which are required for VRLIC
    * LogIntelligence_API_Token = TOKEN taken from logging into VRLIC page

### Configure the Lambda function
In the AWS Web Console, configure an environment variable for the Lambda function. The key of the environment variable should be 'LogIntelligence_API_Token'. The value of the environment variable should be a valid VMware Log Intelligence API token.

#### 1. CloudWatch Logs
In AWS Web Console, add a 'CloudWatch Logs' trigger for the Lambda function. In the configurations of the trigger, specify the CloudWatch log group whose logs you want to collect and send to VMware Log Intelligence. You can add more 'CloudWatch Logs' triggers if you want to send logs of multiple log groups through the Lambda function to VMware Log Intelligence.

#### 2. CloudTrail Logs
If you want to collect CloudTrail logs, then you need to configure CloudTrail to send logs to an S3 bucket.
 Then in the AWS Web Console, add an 'S3' trigger for the Lambda function. In the configurations of the trigger, 
 specify the S3 bucket to which the CloudTrail logs are sent. Add a new environment in lambda with KEY = CloudTrail_Logs and 
 VALUE = true. Once the trigger is configured and enabled, whenever logs go from CloudTrail to the S3 bucket, the Lambda function will be invoked and the logs will be 
 sent to VMware Log Intelligence. You can refer to https://docs.aws.amazon.com/lambda/latest/dg/with-cloudtrail.html for more details.

#### 3. S3 events
Amazon S3 can publish events (for example, when an object is created in a bucket) to AWS Lambda as well as any file(supports .gz, .tar.gz, .zip as well) created in the bucket and invoke your Lambda function by passing the event data as a parameter.
This lambda captures all the Object events like CREATED, DELETED etc. which are provided by AWS 
S3 bucket console. Once the bucket is ready we need to add necessary permissions to lambda to
get access to the s3 bucket and add it as a trigger. https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html
The page describes the process of adding trigger and function roles required for lambda.

#### 4. S3 data
Amazon S3 publish any file(supports .gz, .tar.gz, .zip as well) created in the bucket and invoke your Lambda function by passing the event data as a parameter.
This lambda captures all the Object once it is CREATED, which is provided by AWS 
S3 bucket console. Add a new environment in lambda with KEY = S3Bucket_Logs and VALUE = true
Once the bucket is ready we need to add necessary permissions to lambda to
get access to the s3 bucket and add it as a trigger. https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html
The page describes the process of adding trigger and function roles required for lambda.
Note: Lambda's max execution time is 15 mins. If the file is huge then it lambda might not finish in time and will eventually fail.

#### 5. ELB
We can use AWS Lambda function to push ELB logs once they are configured to be published to S3 bucket.
This lambda captures all the ELB file Object once it is CREATED, which is provided by AWS
S3 bucket console. Once the bucket is ready we need to add necessary permissions to lambda to
get access to the s3 bucket and add it as a trigger. https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html
The page describes the process of adding trigger and function roles required for lambda.

#### 6. VPC
We can use AWS Lambda function to push VPC logs once they are configured to be published to S3 bucket.
This lambda captures all the VPC file Object once it is CREATED, which is provided by AWS
S3 bucket console. Once the bucket is ready we need to add necessary permissions to lambda to
get access to the s3 bucket and add it as a trigger. https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html
The page describes the process of adding trigger and function roles required for lambda.

#### 8. DynamoDB data
We can use a AWS Lambda function to process records in a Amazon DynamoDB Streams stream. With DynamoDB Streams, you can trigger a Lambda function to perform additional work each time a DynamoDB table is updated.
Lambda reads records from the stream and invokes your function synchronously with an event that contains stream records. Lambda reads records in batches and invokes your function to process records from the batch.
https://docs.aws.amazon.com/lambda/latest/dg/with-ddb.html This page explains the 
complete process on how to add trigger dynamoDB to lambda and adding appropriate 
execution roles to access dynamoDB.

#### 9. SQS data
We can use a AWS Lambda function to process data which is pushed to Amazon SQS. With Amazon SQS, you can offload tasks from one component of your application by sending them to a queue and processing them asynchronously.
Lambda polls the queue and invokes your function synchronously with an event that contains queue messages. Lambda reads messages in batches and invokes your function once for each batch. When your function successfully processes a batch, Lambda deletes its messages from the queue.
https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html This link helps in setting up SQS queue
and adding it as a trigger to our lambda function.

#### 10. Kinesis data
First you need to have kinesis stream created in AWS. From the stream created a producer should be registered which pushes the data to kinesis stream. You can build producers for Kinesis Data Streams using the AWS SDK for Java and the Kinesis Producer Library. 
Define specific roles for the kinesis stream to fetch the necessary data. Once the stream is ready we 
can you see this to add trigger to our lambda function. From creating streams to creating producers and pushing data to stream is described here https://docs.aws.amazon.com/streams/latest/dev/introduction.html.

#### 11. SNS data
We can have Lambda functions to process Amazon Simple Notification Service notifications. When a message is published to an Amazon SNS topic, the service can invoke your Lambda function by passing the message payload as a parameter.
https://docs.aws.amazon.com/lambda/latest/dg/with-sns.html This page shows steps to configure SNS topics
and necessary roles to be provided to lambda function. Once the SNS topics and roles are ready, replace the 
lambda code from the code taken in this repo. One use case of SNS data is we can configure RDS events to
flow through SNS messages (https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Events.html).

#### 12. EventBridge data
In AWS Web Console, Create a EventBridge Rule, Select target as 'Lambda Function' and choose the 'Function' using which logs will be injected on VMware vRealize Log Insight Cloud . 
Add a new environment in lambda with KEY = EventBridge_Logs and VALUE = true. Once the trigger is configured and enabled, whenever events go from EventBridge, the Lambda function will be invoked and the logs will be sent to VMware vRealize Log Insight Cloud.
 
## Contributing

The vmware-log-collectors-for-aws project team welcomes contributions from the community. Before you start working with vmware-log-collectors-for-aws, please read our [Developer Certificate of Origin](https://cla.vmware.com/dco). All contributions to this repository must be signed as described on that page. Your signature certifies that you wrote the patch or have the right to pass it on as an open-source patch. For more detailed information, refer to [CONTRIBUTING.md](CONTRIBUTING.md).

## License
Please see [LICENSE.txt](LICENSE.txt).
