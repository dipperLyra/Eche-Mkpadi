## Setting up Kafka and Springboot Kafka Client


Content:
- [What is kafka](#what_is_kafka)
- [Key concepts: kafka server and client(producer and consumer model)](#key_concepts)
- [Installing kafka](#install_kafka)
- [Setting up kafka clients with springboot](#client_setup) 
- [Run the project](#run_project)
- [Conclusion](#conclusion)


### What is Kafka?<a id="what_is_kafka"></a>


[Kafka](http://kafka.apache.org/) is an event streaming platform. This means that in SOA where events from one microservice has to be communicated to another microservice (or other microservices) we can use Kafka to send (stream) these events (messages), analyse them and even log them (store them permanently). Kafka streams these events which means that it captures data in real-time, a continuous flow of data. 


### Key Concepts in Kafka<a id="key_concepts"></a>


1. [Kafka servers](http://kafka.apache.org/documentation/#gettingStarted)

    "Kafka is run as a cluster of one or more servers that can span multiple datacenters or cloud regions."  

    Kafka brokers handle the storage layer of Kafka. This features a log based event storage. While, KafkaConnect handles the integration of other systems into Kafka. For example, KafkaConnect can ingest an entire database.


2. [Kafka Clients]((http://kafka.apache.org/documentation/#gettingStarted))

    Kafka is primarily about events which are ordered in topics. Topics are analogical to folders in filesystems. Clients read and write to Kafka servers through producers and consumers. The producers append (write) events to the log file operated by kafka brokers and the consumers subscribed read the events. 

    It is worth mentioning that even though Kafka implements the publish/subscription system found in message queues or brokers like [RabbitMQ](https://www.rabbitmq.com/) or [ActiveMQ](https://activemq.apache.org/), it is not limited to that. Kafka can store messages, track events, process events and even analyse them. These are not common features of traditional queueing systems which focus solely on writing and reading stream of messages from a broker. 
    
Message queues usually have in-memory storage and messages are deleted once they are consumed. Therefore, Kafka's alternative would be Amazon Kinesis among other full stream processing platforms. 


### Installation and Kafka server stat up<a id="install_kafka"></a>


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

4. Run Kafka server on another terminal

    ```bash
    $ bin/kafka-server-start.sh config/server.properties
    ```


### Setting up Kafka clients with Springboot<a id="client_setup"></a>


1. Generate a Spring application from [here](https://start.spring.io/)

2. Configure maven pom.xml file. This is what mine looks like below:
   
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
      </parent>
      <groupId>com.matukio</groupId>
      <artifactId>mtihani</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>mtihani</name>
      <description>Demo project for Spring Kafka</description>

      <properties>
        <java.version>14</java.version>
      </properties>

      <dependencies>
        <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
          <groupId>org.springframework.kafka</groupId>
          <artifactId>spring-kafka</artifactId>
        </dependency>

        <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-test</artifactId>
          <scope>test</scope>
          <exclusions>
            <exclusion>
              <groupId>org.junit.vintage</groupId>
              <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
          </exclusions>
        </dependency>
        <dependency>
          <groupId>org.springframework.kafka</groupId>
          <artifactId>spring-kafka-test</artifactId>
          <scope>test</scope>
        </dependency>
        <dependency>
          <groupId>com.fasterxml.jackson.core</groupId>
          <artifactId>jackson-databind</artifactId>
        </dependency>
        <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
      </dependencies>

      <build>
        <plugins>
          <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
        </plugins>
      </build>

    </project>
    ```

3. Set up application.properties
    
    ```yaml
      server.port=8081
      
      # producer config
      spring.kafka.producer.bootstrap-servers=localhost:9092
      spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
      spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
      
      # consumer config
      spring.kafka.consumer.bootstrap-servers=localhost:9092
      spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
      spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
      spring.kafka.consumer.group-id=matukio
    ```
    
4. Create producer client

    Producer.java
    
      ```java
        package com.matukio.mtihani.service;

        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.kafka.core.KafkaTemplate;
        import org.springframework.stereotype.Service;

        @Service
        public class Producer {

            private static final Logger logger = LoggerFactory.getLogger(Producer.class);

            private static final String TOPIC = "demo";

            private final KafkaTemplate<String, Object> kafkaTemplate;

            @Autowired
            public Producer(KafkaTemplate<String, Object> kafkaTemplate) {
                this.kafkaTemplate = kafkaTemplate;
            }

            public void sendMessage(String message) {
                logger.info("vvv::  send message");
                kafkaTemplate.send(TOPIC, message);
            }
        }
     ```
 
5. Create consumer client 
        
   Consumer.java
        
    ```java
        package com.matukio.mtihani.service;

        import com.fasterxml.jackson.databind.ObjectMapper;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.kafka.annotation.KafkaListener;
        import org.springframework.stereotype.Service;

        import java.io.IOException;

        @Service
        public class Consumer {

            private static final Logger logger = LoggerFactory.getLogger(Consumer.class);

            @Autowired
            private final ObjectMapper mapper = new ObjectMapper();

            @KafkaListener(topics = "demo", groupId = "matukio")
            public void consume(String message) throws IOException {
                logger.info("consumed message is= " + message);
            }
        }
     ```

6. Create a controller

   MessagingController.java
        
   ```java
        
    package com.matukio.mtihani.controller;

    import com.matukio.mtihani.service.Producer;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    @RequestMapping("/messaging")
    public class MessagingController {

    private final Producer producer;

    @Autowired
    MessagingController(Producer producer) {
        this.producer = producer;
    }

    @PostMapping(value = "/publish")
    public void sendMessageToKafkaTopic(@RequestParam("message") String message) {
        this.producer.sendMessage(message);
    }
    }
    ```
        
        
### Run the application<a id="run_project"></a>


` mvn exec:java -Dexec.mainClass=com.matukio.mtihani.MtihaniApplication`

Using curl or any other tool make a request with "message" in the body. Then, check terminal where MtihaniApplication is running for the output.

        
        
### Conclusion<a id="conclusion"></a>  


This post assumes that you have decided to use the SOA (Service Oriented Architecture or popularly known as microservices) for your application. It does not attempt to argue whether that is the better architecture or not. 

But a quick check could be to answer the following questions below:

1. Do I intend to deploy my application on several cloud zones?
2. Do I process enough data that necessitates that I partition and replicate my database?
3. Do I necessarily need to break and deploy my application as separate pieces. 

If your answer is yes these questions, then, you might want to use an SOA. Otherwise, I think the monolith approach will save you a lot of time. 

This is a basic set up and it is not ideal. The goal is to introduce the reader to kafka and how to set up kafka clients on springboot.

In this set up the producer and consumer live in the same project whereas in real applications the producer and consumer would usually be in different microservices located in different machines.

