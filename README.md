# FactoryPal Streaming Pipeline

## Architecture
## How to run

## Components
### APIs
This section describes APIs created to stream data to our streaming system. 

* The APIs will use `192.168.0.1` as IP host & port `5000` on the network.
* Each api will have an end point of `stream_data_{file_name}`
* APIs will stream the data using only Json files as source.
* The APIs will stream each data point (json object) with a time interval.
    
    * By default the delay is set to 1 sec
    * You can change it from the docker-compose file > streaming_api service > change the float in the `python src/app.py {your_input}` > you need to build again
* Data will be streamed according to the time element of each json object.
Two REST APIs will be implemented in a backend service:

1. **Metrics API**: Will stream data from `metrics.json` folder in the data_source dir. Access it using: http://192.168.0.1:5000/stream_data_metrics

2. ** workorder API**: Will stream data from `workorder.json` folder in the data_source dir. Will stream using endpoint. Access it using: http://192.168.0.1:5000/stream_data_workorder

### Kafka
This section describes the Kafak component in the Pipeline.

#### KafkaAdmin:
connects to the kafka cluster and performs different admin functions such as create, delete topics, also kafka config can be set such as partition number (`Default 3`) and replication factor (`Default 1`).
* Auto creation is disabled on the cluster so data will not be consumed by the broker unless a topic is created first. I set like this for security reasons obviously `:(`.
* The Kafak cluster depends on Zookeeper running on `192.168.0.2:2181`
* Kafka Bootstrap Server is on bootstrap server is on: `192.168.0.3:9092`

#### ProducerABS:
An abstraction to create a producer to consume from APIs and write to kafka broker(s). To create a new producer:
1. Create a child class from `ProducerABC` by:
    
    * Setting the `end point` of the API to consume from it.
    * Setting the topic name to produce to it (use the KafkaAdmin to create one if it does not exists)
    * Setting a `key` to use for message partitioning. (Optional)
2. Add the class to the produce_cmd in cmd directory.

* You can check the messages of a topics on the consul:

    1. Login into the kafka broker container:
        ```
        docker-compose exec kafka  bash
        ```
    2. execute:
    ``` opt/bitnami/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic <topic_name> ```
