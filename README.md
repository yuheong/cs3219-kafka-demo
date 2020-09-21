# Setup and testing of Apache Kafka with multiple brokers

## Run docker containers

Firstly, spin up the container with Zookeeper and three Kafka brokers with the command: ```docker-compose up```

The kafka-demo container should be running successfully, with zookeeper, kafka1, kafka2 and kafka3 containers running inside.


## Testing of Pub-Sub (Producer-Consumer) and fault tolerance

With the kafka-demo container running, open two terminal windows and enter any two of the running kafka containers,

e.g. ```docker exec -it kafka1 /bin/bash```

Once inside the running container, execute ```cd /opt/kafka/bin``` to locate the Kafka support scripts.

Create a topic called message with 1 partition and replication factor 2:

```./kafka-topics.sh --bootstrap-server kafka1:9091 --create --topic stuff --partitions 1 --replication-factor 2```

Then, list the topics and the topic 'stuff' should be present:

```./kafka-topics.sh --bootstrap-server kafka1:9091 --list```

To find out more information about the topic such as its leader and replica brokers, execute the command:

```./kafka-topics.sh --bootstrap-server kafka1:9091 --topic stuff --describe```

On the other running kafka container (e.g. kafka2), start a consumer to listen to messages from the stuff topic:

```./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic stuff --from-beginning```

Now, back in the first terminal with kafka1, start a producer to send publish messages to the stuff topic:

```./kafka-console-producer.sh --broker-list localhost:9091 --topic client```

Once messages are typed and sent in the producer, the consumer should instantly log the messages to the output, demonstrating that the pub/sub messaging is successful.

To test fault tolerance, we can shut down a kafka broker container and start the consumer on the remaining brokers to verify that the topic is being replicated and is fault-tolerant.

In addition, we can also stop the leader broker of the 'stuff' topic and run the command to describe the topic again, and it will update the leader broker to one that is still running.