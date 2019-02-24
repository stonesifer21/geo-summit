# geo-summit
This repository is for the Unleash the Power of Serverless Architecture at the GeoDev Summit at Harrisburg University on March 4, 2019.

Instructions:

1.	Create ElasticSearch Service
a.	Name = beers-and-hobbies-search
b.	Set authentication to Open Public Access (no vpc)
2.	Create DynamoDB table
a.	Table Name = beers-and-hobbies-people
b.	Primary Key = personID
c.	DynamoDB table Name and primary key must match ElasticSearch scripts
d.	After created > Manage Stream
3.	Setup Roles in IAM
a.	Create Role > AWSLambdaBasicExecutionRole
b.	Name:  geo-dev-role
c.	Add Inline Policies
	Access DynamoDB Table
{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Action": [
	                "dynamodb:DescribeTable",
	                "dynamodb:Scan"
	            ],
	            "Effect": "Allow",
	            "Resource": [
	                "arn:aws:dynamodb:us-west-2:XXXXXXXXXXXX:table/elements"
	            ]
	        }
	    ]
	}

Access DynamoDB Stream
{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Action": [
	                "dynamodb:DescribeStream",
	                "dynamodb:GetRecords",
	                "dynamodb:GetShardIterator",
	                "dynamodb:ListStreams"
	            ],
	            "Effect": "Allow",
	            "Resource": [
	                "arn:aws:dynamodb:us-east-1:XXXXXXXXXXXX:table/elements/stream/2017-09-14T14:16:12.788"
	            ]
	        }
	    ]
	}
Access to ElasticSearch
{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Action": [
	                "es:ESHttpPost"
	            ],
	            "Effect": "Allow",
	            "Resource": "arn:aws:es:us-east-1:XXXXXXXXXXXXX:domain/elementsdomain/*"
	        }
	    ]
	}

4.	Create Lambda function from Scratch
a.	Name:  index-dating-app-search-service
b.	Choose role just created
c.	Create environment variable using ELASTICSEARH ENDPOINT
5.	Use Postman create ElasticSearch mappings
a.	Use name of DynamoDB and append ElasticSearch End point with DynamoDB name
b. Replace URL and append URL end with repository name:  ex:  https://search-geo-summit-backup-7azns2n3fe7kpksnfjr7go62ae.us-east-1.es.amazonaws.com/geodev-summit-backup
c.	Verify Indices
6.	Now create value in DynamoDB
{
  "personID": "100",
  "location": "40.183450, -76.921845",
  "zipcode": "17339",
  "city": "Lewisberry",
  "name": "Jordan Biskit",
  "gender": "F",
  "hobbies": "Tennis"
}
7.	Verify value loaded into Elasticsearch
8.	Now batch load using CLI
a.	Navigate to directory where file is located.
aws dynamodb batch-write-item --request-items file://load-people.json
