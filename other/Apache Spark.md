Athena uses Presto to query a filesystem  (eg Parquet format) - through a metastore catalog developed by AWS Glue, which creates a data dictionary mapping from file to table - via SQL.

Spark queries a filesystem (eg Parquet file format in Hadoop Distributed File System - HDFS) via dataframes with Hive metastore. 
Spark can also read Parquet file format directly

-eg
```python
val df = spark.read.format("parquet").load("s3://s3-parquet-file-folder")

df.show()

df.createOrReplaceTempView("asset_dim")

val df2 = spark.sql("select count(1) from asset_dim")

df2.show()
```


Athena SQL => Presto (reads AWS Glue metastore) => Parquet

Spark SQL => Spark SQL library (reads AWS Glue Hive metastore) => Parquet

These Analytics engines (Spark and Presto) process data from files to relational tables via SQL in memory. There is a cost to translating various formats (each Hadoop-style subsystem like EMR Hortonworks or Spark vs Presto) called Serde, which means serialization and deserialization. 

Apache Arrow and Flight is an in-memory format of handling data flows (files queried with SQL) universally so they don't need to be (de)serialized back and forth between every subsystem and framework.