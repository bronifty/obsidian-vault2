1 order history app
- server logs => kinesis => lambda => dynamodb => app
2 product recommendations
- logs => kinesis firehose => s3 => emr
3 transaction rate alarm
- logs -> kinesis streams => kinesis analytics => kinesis streams => lambda => sns
4 near real time log analysis
- logs => kinesis firehose => elastisearch
5 data warehousing & visualization
- logs => firehose => s3 => glue => athena
					 | => redshift => quicksight |
