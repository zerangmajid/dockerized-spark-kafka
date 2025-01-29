# Kafka Consumer Integration

This document provides details on implementing and using a Kafka consumer in the *create-kafka-consumer** step of the project.

## Overview

In this step, a Kafka consumer is created to consume messages from a Kafka topic. The consumer will process and print the messages in real time.

## Prerequisites

1. A running Kafka Broker and Zookeeper.
2. Kafka topic(s) created and populated with messages.
3. Python and required libraries installed.

## Kafka Consumer Example

Below is an example Python script to create a Kafka consumer:

```python
from kafka import KafkaConsumer
from json import loads

consumer = KafkaConsumer(
    'your-topic-name',
    bootstrap_servers=['kafka-broker:29092'],
    auto_offset_reset='earliest',
    enable_auto_commit=True,
    group_id='your-group-id',
    value_deserializer=lambda x: loads(x.decode('utf-8'))
)

for message in consumer:
    print(f"Consumed message: {message.value}")
```

## How to Run

1. Ensure Kafka and Zookeeper are running.
2. Replace `'your-topic-name'` and `'your-group-id'` in the script with your topic and group ID.
3. Run the script:

   ```bash
   python consumer.py
   ```

4. Observe the messages being consumed in real time.

## Notes

- The `auto_offset_reset` parameter can be set to `earliest` or `latest` based on when you want the consumer to start reading messages.
- The `group_id` is essential for Kafka's consumer group management.

## Troubleshooting

- If the consumer is not receiving messages, ensure the Kafka topic has messages and is correctly named in the script.
- Check the Kafka Broker's logs for errors.

## Next Steps

Proceed to **session16-3-prepare-df-read-stream-spark** to process the consumed messages in Apache Spark.
