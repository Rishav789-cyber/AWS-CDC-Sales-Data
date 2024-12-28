# AWS-CDC-Sales-Data
![AWS CDC Sales Data Project drawio](https://github.com/user-attachments/assets/3423c17a-260f-43ed-8f7a-261cd69b2281)

# Architecture Overview:

## 1.Data Generation (Mock Data):
Python Script (Mock Data Generator): This script simulates sales data and stores it in an AWS DynamoDB orders table. This table acts as the source of the Change Data Capture (CDC) data.

## 2.Change Data Capture (CDC) via DynamoDB Streams:
DynamoDB Streams: Captures changes (inserts, updates, deletes) in the DynamoDB orders table and emits them to AWS EventBridge to be processed downstream.

## 3.EventBridge Pipe (Integration):
AWS EventBridge Pipes: This service connects DynamoDB Streams (CDC source) with Kinesis Stream, enabling the flow of change data into Kinesis.

## 4.Kinesis Stream and Firehose:
Kinesis Stream: Captures raw CDC data (insert, modify, delete events) from DynamoDB Streams via EventBridge.
Kinesis Firehose: Firehose batches and buffers the data from Kinesis Stream before sending it downstream for processing.

## 5.Data Transformation with AWS Lambda:
AWS Lambda: After the CDC data is delivered to Kinesis Firehose in batches, a Lambda function is triggered. The function:
-Transforms the raw data (removes unnecessary metadata).
-Extracts relevant order details, adds the eventName (insert, modify, delete), and a timestamp for the event.
The Lambda function then forwards the transformed data back to Kinesis Firehose.

## 6.Data Storage in Amazon S3:
Kinesis Firehose: Firehose delivers the transformed data to Amazon S3, where the sales data is stored

## 7.Metadata Management using AWS Glue:
AWS Glue Data Catalog: AWS Glue is used to crawl the data stored in S3 and populate the Glue Data Catalog, which serves as the metadata repository for the stored data.

## 8.Data Querying with Amazon Athena:
Amazon Athena: You can query the data stored in S3 using SQL through Athena, leveraging the schema defined in the Glue Data Catalog to perform ad-hoc analytics.
