# Kafka Producer Using Jupyter Notebook

This document provides a step-by-step guide to running a Kafka Producer using Jupyter Notebook and verifying the results.

## Steps to Run the Producer and Verify Results

### 1. Access Jupyter Notebook

1. Run the following command to access the logs of the Jupyter Notebook container:
   ```bash
   docker logs pyspark
   ```

2. Copy the URL for Jupyter Notebook from the logs (e.g., `http://127.0.0.1:8888/?token=...`) and open it in your web browser.

### 2. Create or Load a Notebook

1. Once Jupyter Notebook is open, create a new notebook or open an existing one.
2. Add the following Kafka Producer script to the notebook:

   ```python
   import time
   import requests
   import re
   from pprint import pprint
   from json import dumps
   from kafka import KafkaProducer

   # Kafka producer configuration
   producer = KafkaProducer(
       bootstrap_servers=['kafka-broker:29092'],
       value_serializer=lambda x: dumps(x).encode('utf-8'),
       key_serializer=str.encode
   )

   # Kafka topic name
   TOPIC_NAME = 'example-topic'

   # Example of sending data
   for i in range(10):
       producer.send(TOPIC_NAME, value={'key': i, 'value': f'Message {i}'})
       print(f"Sent message {i}")
       time.sleep(1)
   ```

3. Run the cells to execute the producer script.

### 3. Verify Messages in KafkaHQ

1. Open KafkaHQ at [http://localhost:9080](http://localhost:9080).
2. Navigate to the **Topics** section.
3. Select the topic `example-topic` to view the messages produced by the Kafka Producer.

### 4. Observe the Output

1. In the Jupyter Notebook, you will see logs of messages being sent:
   ```plaintext
   Sent message 0
   Sent message 1
   Sent message 2
   ...
   ```
   ![image](https://github.com/user-attachments/assets/b6ae5457-c40d-4009-b7d8-2e397d61c6f3)
![image](https://github.com/user-attachments/assets/b4727a99-aa27-4892-92cc-7b7c5e2bd15f)


3. In KafkaHQ, you will see the messages stored in the topic with their respective keys and values.

## Additional Notes

- Ensure Kafka and Zookeeper services are running before starting the producer.
- Modify the topic name and message content as needed for your use case.
- If the producer encounters errors, check the Kafka Broker logs for troubleshooting.

## Next Steps

Proceed to the next stage to consume and process these messages using Kafka Consumer.
