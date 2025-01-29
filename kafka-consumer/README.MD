# Kafka Producer Using Jupyter Notebook

This document provides a step-by-step guide to running a Kafka Producer using Jupyter Notebook and verifying the results.

## Steps to Run the Producer and Verify Results

### 1. Access Jupyter Notebook

1. Run the following command to access the logs of the Jupyter Notebook container:
   ```bash
   docker logs pyspark
   ```
![pyspark Screenshot]( https://github.com/zerangmajid/dockerized-spark-kafka/blob/b612a748e836a69842b5a209faca912c71d419c9/Images/logs%20pyspark.png?raw=true)
 
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

   def filterData(item):
       cleansed_data = {
           "id": item["id"],
           "content": item["content"],
           "sendTime": item["sendTime"],
           "sendTimePersian": item["sendTimePersian"],
           "senderName": item["senderName"],
           "senderUsername": item["senderUsername"],
           "type": item["type"],
           "raw-data": item
       }
       return cleansed_data

   producer = KafkaProducer(
       bootstrap_servers=['kafka-broker:29092'],
       value_serializer=lambda x: dumps(x).encode('utf-8'),
       key_serializer=str.encode
   )

   TOPIC_NAME = "SahamYab-Session_16"

   url = "https://www.sahamyab.com/guest/twitter/list?v=0.1"
   delay = 10
   cnt = 0

   while True:
       try:
           response = requests.request('GET', url, headers={'User-Agent': 'Chrome/116'})
           if response.status_code == requests.codes.ok:
               items = response.json()['items']
               for item in items:
                   cnt += 1
                   print(f"-- {cnt:4} - {item['id']}")
                   if not item.get('content'):
                       print("tweet is unacceptable")
                       continue
                   else:
                       data = filterData(item)
                       producer.send(TOPIC_NAME, value=data, key=item['id'])
           else:
               print(f"Error in fetch data: {response.status_code}")
           time.sleep(delay)
       except Exception as e:
           print(e)
           continue
   ```

3. Run the cells to execute the producer script.

 

### 3. Verify Messages in KafkaHQ

1. Open KafkaHQ at [http://localhost:9080](http://localhost:9080).
2. Navigate to the **Topics** section.
 

3. Select the topic `example-topic` to view the messages produced by the Kafka Producer.

### 4. Observe the Output

1. In the Jupyter Notebook, you will see logs of messages being sent:
   ```plaintext
   --   1 - 453132607
   --   2 - 453132608
   tweet is unacceptable
   --   3 - 453132609
   ...
   ```
2. In KafkaHQ, you will see the messages stored in the topic with their respective keys and values.

 ![hashtag Screenshot]( https://github.com/zerangmajid/dockerized-spark-kafka/blob/d8d1db635729ed4893fdb56211349339ba68bcb8/Images/ConsumerWithHashashtag.png?raw=true)

## Additional Notes

- Ensure Kafka and Zookeeper services are running before starting the producer.
- Modify the topic name and message content as needed for your use case.
- If the producer encounters errors, check the Kafka Broker logs for troubleshooting.

## Next Steps

Proceed to the next stage to consume and process these messages using Kafka Consumer.
