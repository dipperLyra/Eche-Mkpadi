## Setting up Kafka and Springboot Kafka Client

draft
- what is kafka
- key concepts: explain kafka broker, producer and consumer model
- installing kafka
- run kafka
- setting kafka clients with springboot. 
- conclusion

### What is Kafka?

[Kafka](http://kafka.apache.org/) is an event streaming platform. This means that in SOA where events from one microservice has to be communicated to another microservice (or other microservices) we can use Kafka to send (stream) these events (messages), analyse them and even log them (store them permanently). Kafka streams these events which means that it captures data in real-time, a continuous flow of data. 

### Key Concepts in Kafka

1. [Kafka servers](http://kafka.apache.org/documentation/#gettingStarted)
"Kafka is run as a cluster of one or more servers that can span multiple datacenters or cloud regions."  

Kafka brokers handle the storage layer of Kafka. This features a log based event storage. While, KafkaConnect handles the integration of other systems into Kafka. For example, KafkaConnect can ingest an entire database.
A 
2. Kafka Clients
Kafka is primarily about events which are ordered in topics. Topics are analogical to folders in filesystems. Clients read and write to Kafka servers through producers and consumers. The producers append (write) events to the log file operated by kafka brokers and the consumers subscribed read the events. 

It is worth mentioning that even though Kafka implements the publish/subscription system found in message queues or brokers like RabbitMQ or ActiveMQ, it is not limited to that. Kafka can store messages, track events, process events and even analyse them. These are not common features of traditional queueing systems which focus solely on writing and reading stream of messages from a broker. Their storage is usually in-memory and messages are deleted once consumed. Therefore, Kafka's alternative would be Amazon Kinesis among other full stream processing platforms. 


### Installation and Kafka server stat up

1. Download Kafka [here](https://downloads.apache.org/kafka/2.6.0/kafka_2.13-2.6.0.tgz) 

2. Extract kafka,  and Kafka server.

```bash
$ tar -xzf kafka_2.13-2.6.0.tgz
$ cd kafka_2.13-2.6.0
```

3. Run the Zookeeper

```bash
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

4. Run Kafka server

```bash
$ bin/kafka-server-start.sh config/server.properties
```

### Setting up Kafka clients with Springboot

1. Generate a Spring application from [here](https://start.spring.io/)

2. Configure maven pom.xml file

You can use the [editor on GitHub](https://github.com/dipperLyra/spring-kafka-setup.github.io/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.


