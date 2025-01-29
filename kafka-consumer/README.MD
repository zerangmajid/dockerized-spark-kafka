# Kafka Consumer Using Jupyter Notebook

This document provides a step-by-step guide for running a Kafka Consumer to process messages and send the results to a new Kafka topic.

## Overview

In this step, we use a Kafka Consumer to:

1. Read messages from a Kafka topic.
2. Extract hashtags from the message content.
3. Send the updated messages to a new Kafka topic.

![Kafka Topics in KafkaHQ](sandbox:/mnt/data/image.png)

The updated messages will include a new field for hashtags, as shown in the example message below:

```json
{
  "id": "455497282",
  "content": "#تجزیه الگوری و اهداف در دفترچه!",
  "sendTime": "2025-01-29T06:31:00Z",
  "sendTimePersian": "1403/11/10 10:01",
  "senderName": "شایان",
  "senderUsername": "shayan11",
  "type": "twit",
  "hashtags": [
    "#تجزیه"
  ]
}
```

## Partitions and Consumers

1. **Partitions and Consumer Groups:**
   - Each partition in Kafka can only be read by one consumer from a consumer group.

![Partition and Consumers Explanation](sandbox:/mnt/data/image.png)

## Steps

### 1. Setup Kafka Consumer in Jupyter Notebook

Add the following script to your Jupyter Notebook:

```python
import time
import requests
import re
from pprint import pprint
from json import loads, dumps
from kafka import KafkaConsumer, KafkaProducer

# Kafka Topic Setup
INPUT_TOPIC_NAME = "SahamYab-Session_16"
OUTPUT_TOPIC_NAME = "SahamYab-Session_16_Hashtags"
KAFKA_SERVER = "kafka-broker:29092"

# Function to extract hashtags from a message
def get_tags(text):
    tags = re.findall(r"#\w+", text)
    return tags

# Kafka Consumer Configuration
consumer = KafkaConsumer(
    INPUT_TOPIC_NAME,
    bootstrap_servers=[KAFKA_SERVER],
    auto_offset_reset='earliest',
    enable_auto_commit=True,
    group_id='hashtag-group',
    value_deserializer=lambda x: loads(x.decode('utf-8')),
    key_deserializer=lambda x: x.decode('utf-8'),
    client_id='consumer-of-producer'
)

# Kafka Producer Configuration
producer = KafkaProducer(
    bootstrap_servers=[KAFKA_SERVER],
    value_serializer=lambda x: dumps(x).encode('utf-8'),
    key_serializer=str.encode
)

# Process Messages
for message in consumer:
    try:
        data = message.value
        print(f"Processing Tweet ID {message.key}")
        print(f"Content: {data['content']}")

        # Extract hashtags
        tags = get_tags(data['content'])
        print(f"Tags: {tags}")

        # Update the message data with hashtags
        data['hashtags'] = tags

        # Send updated message to the new topic
        producer.send(OUTPUT_TOPIC_NAME, value=data, key=data['id'])
    except Exception as e:
        print(f"Error: {e}")

```

### 2. Running the Notebook

1. Start the Jupyter Notebook server using the command:
   ```bash
   docker logs pyspark
   ```
   Open the Jupyter Notebook URL provided in the logs.

2. Add the script above to your notebook and run it.
3. The consumer will start processing messages from the input topic and log the results.

### 3. Verify Processed Messages

1. Open KafkaHQ at [http://localhost:9080](http://localhost:9080).
2. Navigate to the `SahamYab-Session_16_Hashtags` topic.
3. Verify that messages with extracted hashtags have been successfully published.

![KafkaHQ Consumer Lag](sandbox:/mnt/data/image.png)

### Sample Output

Logs in Jupyter Notebook will show:
```plaintext
Processing Tweet ID 453132607
Content: "Sample tweet content with #hashtags."
Tags: ['#hashtags']
...
```

### Notes

- Ensure Kafka and Zookeeper services are running before starting the consumer.
- Customize the input and output topics as per your project requirements.
- Handle exceptions in production environments to avoid consumer crashes.

### Next Steps

Proceed to visualize or process the data further in the next stage of your pipeline.
