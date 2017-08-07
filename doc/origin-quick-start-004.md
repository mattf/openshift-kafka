# Quick Start Explained ( part 4 of 4)

## Follow the [Apache Kafka Documentation Quick Start](https://kafka.apache.org/documentation.html#quickstart)


1. Create a topic

``` bash
bin/kafka-topics.sh --create --zookeeper apache-kafka --replication-factor 1 --partitions 1 --topic test
```

1. List topics

``` bash
bin/kafka-topics.sh --list --zookeeper apache-kafka
```

1. Send some messages

``` bash
bin/kafka-console-producer.sh --broker-list apache-kafka:9092 --topic test <<EOF
foo
bar
baz
EOF
```

1. Receive some messages

``` bash
bin/kafka-console-consumer.sh --bootstrap-server apache-kafka:9092 --topic test --from-beginning
```

## Credits

* This is based on work original by [Jim Minter](https://github.com/jim-minter) and a fork of https://github.com/mattf/openshift-kafka by [Matthew Farrellee](https://github.com/mattf)
