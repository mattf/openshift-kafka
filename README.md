# "Apache Kafka for OpenShift"

Run [Apache Kafka](https://kafka.apache.org/) and [Apache ZooKeeper](https://zookeeper.apache.org/) on [OpenShift v3](https://www.openshift.com/).

This was developed for demonstration purposes. Use in environments where you do not need persistence in the face of failures or configuration tuning for scale.

The architecture is as follows:

* 1 pod containing a container for Apache Kafka and Apache ZooKeeper
* 1 service to access the containers


## Quick start

1. Load resources (Templates, DeploymentConfigs, Services)
```bash
oc create -f https://raw.githubusercontent.com/mattf/openshift-kafka/master/resources.yaml
```

2. Deploy the Apache Kafka + Apache Zookeeper pod
```bash
oc new-app apache-kafka
```

## Follow the [Apache Kafka Documentation Quick Start](https://kafka.apache.org/documentation.html#quickstart)

3. Deploy a debugging container and connect to it
```bash
oc run -it --rm kafka-debug --image=mattf/openshift-kafka --command -- bash
```

4. Create a topic
```bash
bin/kafka-topics.sh --create --zookeeper apache-kafka --replication-factor 1 --partitions 1 --topic test
```

5. List topics
```bash
bin/kafka-topics.sh --list --zookeeper apache-kafka
```

6. Send some messages
```bash
bin/kafka-console-producer.sh --broker-list apache-kafka:9092 --topic test <<EOF
foo
bar
baz
EOF
```

7. Receive some messages
```bash
bin/kafka-console-consumer.sh --bootstrap-server apache-kafka:9092 --topic test --from-beginning
```

## Credits

* This is based on work original by [Jim Minter](https://github.com/jim-minter)
