# Prepare DataFrame with Spark Structured Streaming

This document provides guidance on setting up a Spark Structured Streaming pipeline to read data from a Kafka topic and process it in Apache Spark.

## 1. Verify Spark Cluster Setup

Ensure your Spark cluster is up and running. The Spark Master UI should display active workers, as shown below:

 ![Spark Master UI](https://github.com/zerangmajid/dockerized-spark-kafka/blob/29c9f20424ecb41f05660968db301b84cb170b36/Images/spark.png?raw=true)


## 2. Configure Kafka in Spark

To enable Spark to communicate with Kafka, add the required Kafka package to the Spark environment:

```python
import os

os.environ['PYSPARK_SUBMIT_ARGS'] = '--packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.4.1 pyspark-shell'
```

> **Note:** Ensure the Kafka package version matches your Spark version. 

![Kafka Configuration in Spark](sandbox:/mnt/data/image.png)

## 3. Create a DataFrame from Kafka

Using Spark's Structured Streaming API, read data from a Kafka topic:

```python
# This cell creates a DataFrame that reads data from a Kafka topic using Spark's Structured Streaming API.
df = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", KAFKA_SERVER) \
    .option("subscribe", TOPIC_Step2_NAME) \
    .option("startingOffsets", "earliest") \
    .load()
```

- The `readStream` method initializes a streaming DataFrame.
- The `format("kafka")` specifies Kafka as the data source.
- The `startingOffsets` option ensures data is read from the beginning of the topic.

![Streaming DataFrame Code](sandbox:/mnt/data/image.png)

## 4. Understanding Lazy Execution in Spark

Apache Spark uses the concept of **Lazy Execution**, meaning transformations like reading or processing data are not executed until an action like `count`, `write`, or `collect` is invoked.

### Example Code

```python
df = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", KAFKA_SERVER) \
    .option("subscribe", TOPIC_Step2_NAME) \
    .option("startingOffsets", "earliest") \
    .load()
```

![Lazy Execution Explanation](sandbox:/mnt/data/image.png)

## 5. Batch Processing Configuration

In this step, Spark processes tweets in streaming mode using batches. The number of records in each batch can be controlled using the `maxOffsetsPerTrigger` option.

```python
df = df \
    .option("maxOffsetsPerTrigger", 100) \
    .load()
```

This allows you to control the rate of data ingestion for processing.

![Batch Configuration](sandbox:/mnt/data/image.png)

## 6. Monitoring Completed Jobs

Spark allows you to monitor the progress and performance of your streaming jobs using the Spark UI. Below is an example of the completed jobs:

![Completed Jobs in Spark UI](sandbox:/mnt/data/image.png)

- Each row represents a completed job (or batch) in the streaming pipeline.
- The `Duration` column shows the time taken to process each batch.
- The `Tasks` column provides details about the number of tasks executed and their success status.

For example, if the batch size is set to 100, each job processes up to 100 records. This setting ensures optimal performance by balancing the processing load and latency.

## Additional Notes

- **Monitoring Spark Jobs:** Use the Spark Master UI to monitor running and completed jobs.
- **Kafka Configuration:** Ensure Kafka and Zookeeper are running before starting the pipeline.
- **Error Handling:** Add proper exception handling to manage streaming errors efficiently.

With these steps, you can create a robust Spark Structured Streaming pipeline to process Kafka data effectively.
