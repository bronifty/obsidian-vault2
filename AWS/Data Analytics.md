- define aws data analytics services and understand how they integrate with one another
- explain how aws data analytics services fit in the data lifecycle of collection, storage, processing, and visualization

### Exam Blueprint
1) collection (streaming, batch, source, target) - 18%
2) storage and data management (operational characteristics of storage solution like s3, redshift, emr, elastisearch, dynamodb; access and retrieval patterns) - 22%
3) processing (determine various data processing solutions for various scenarios; design solution for transforming data and preparing data for analysis ) - 24%
4) analysis and visualization (quicksight) - 18%
5) security (auth) - 18%

Course Topics:
- s3
- rds
- streaming collection kinesis/kafka
- collection load
- redshift
- emr
- glue athena quicksight
- elastisearch
- security services

### s3 
- transfer acceleration is $0.04/GB in US, EU, JAP and $0.08/GB elsewhere
	- enabled per bucket
	- endpoints: bucketname.s3-accelerate.amazonaws.com (ipv4) | bucketname.s3-accelerate.dualstack.amazonaws.com (ipv6)

[aws s3 cli ref](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html)
```shell
aws s3 cp cli.txt s3://das-demos/cli.txt
aws s3 mv movemeCLI.txt s3://das-demos
aws s3 ls das-demos
aws s3 sync . s3://das-demos
aws s3 sync s3://das-demos .
aws s3 rb s3://bronifty-testbucket-12345 --force
aws s3 mb s3://bronifty
```
> [!INFO]- boto3 multipart upload
>  ![[boto3-multipart-upload]]

- obsidian://open?vault=Obsidian%20Vault&file=scripts%2Fpython%2Fboto3%20multipart%20upload%20to%20aws%20s3
- [multer s3 multipart upload node and react](https://github.com/bronifty/s3-upload-v3)
- Storage classes
	- Standard
	- Intelligent Tiering (management engine)
	- Infrequent access
	- one zone IA
	- glacier archive
	- glacier deep archive