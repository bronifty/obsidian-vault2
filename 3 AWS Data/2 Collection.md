
- Collection 
	- Real Time - Immediate actions
		- Kinesis Data Streams (KDS)
		- Simple Queue Service (SQS)
		- Internet of Things (IoT)
	- Near Real Time - Reactive actions
		- Kinesis Data Firehose (KDF)
		- Database Migration Service (DMS)
	- Batch - Historical Analysis
		- Snowball
		- Data Pipeline

### Kinesis Data Streams
- streams come in and are pushed to many consumers
- Producers
	- apps client sdk kpl kinesis agent
	- Record<Partition Key, Data Blob (<=1MB)>
		- Partition Key determines which shard gets the data blob
- Streams
		- Data transfer rate is 1MB/sec or 1000 msg/sec per shard
			- 6 shards = 6 MB/sec
- Consumers
	- Record<Partition Key, Sequence no., Data Blob>
		- 2 MB/sec (shared) per shard all consumers or 2 MB/sec (enhanced) per shard per consumer
	- Apps (SDK or KCL), Lambda, Kinesis Data Firehose, Kinesis Data Analytics
- retention <= 1 year
- can replay (reprocess) data
- immutable (once data inserted can't be deleted)
- same partition same shard (ordering)

### Duplicates
- app PUTs record into Stream as sequence # with Data  