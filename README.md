# dockerized-spark-kafka
# Spark & Kafka Integration

This project demonstrates the integration of Apache Spark and Apache Kafka using Docker Compose. The setup includes Spark Master, Spark Workers, PySpark, Zookeeper, Kafka Broker, Schema Registry, and KafkaHQ for monitoring Kafka topics.

## Prerequisites

1. Docker and Docker Compose installed.
2. Basic understanding of Kafka and Spark.

## Services Overview

- **Spark Master**: Manages Spark cluster resources.
- **Spark Workers**: Executes tasks assigned by Spark Master.
- **PySpark**: Provides Jupyter Notebook interface to submit Spark jobs.
- **Zookeeper**: Coordinates distributed services.
- **Kafka Broker**: Manages Kafka topics and message streams.
- **Schema Registry**: Manages schemas for Kafka messages.
- **KafkaHQ**: A UI for Kafka management and monitoring.

## Setup Instructions

1. Clone this repository:

   ```bash
   git clone https://github.com/zerangmajid/dockerized-spark-kafka.git
   cd dockerized-spark-kafka

   ```

2. Build and start the containers:

   ```bash
   docker-compose up --build
   ```

3. Access the services:

   - **Spark Master**: [http://localhost:8080](http://localhost:8080)
   - **Spark Worker 1**: [http://localhost:8081](http://localhost:8081)
   - **Spark Worker 2**: [http://localhost:8082](http://localhost:8082)
   - **PySpark Jupyter Notebook**: [http://localhost:8888](http://localhost:8888)
   - **KafkaHQ**: [http://localhost:9080](http://localhost:9080)

## Monitoring Kafka Topics

1. Open KafkaHQ at [http://localhost:9080](http://localhost:9080).
2. Navigate to the **Topics** section to view the messages and topic details.

## Docker Compose Configuration

The `docker-compose.yml` file defines the services and their configurations. Key highlights:

- **Spark Master and Workers** share a network for seamless communication.
- **Kafka Broker** is configured with Zookeeper and Schema Registry.
- **KafkaHQ** connects to Kafka Broker and Schema Registry for monitoring.

## Additional Notes

- Modify the `docker-compose.yml` file as per your requirements.
- Ensure the required ports are not blocked by other applications.

## Contributing

Feel free to open issues or create pull requests to improve this project.

## License

This project is licensed under the MIT License.
