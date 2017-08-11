# Quick Start Explained ( part 2 of 4)
## Load resources (Templates, DeploymentConfigs, Services)
```bash
oc create -f https://raw.githubusercontent.com/rondinif/openshift-kafka/master/resources.yaml
```

``` yaml
kind: List
apiVersion: v1
metadata: {}

items:

- apiVersion: v1
  kind: Template
  metadata:
    name: apache-kafka
    annotations:
      description: 1-pod Apache Kafka
      tags: messaging,streaming,kafka
  parameters:
  - name: NAME
    description: Name prefix for each object created
    required: true
    value: apache-kafka
  - name: IMAGE
    description: Image with Apache Kafka and Apache ZooKeeper
    required: true
    value: rondinif/openshift-kafka
  objects:
  - apiVersion: v1
    kind: DeploymentConfig
    metadata:
      name: ${NAME}
    spec:
      replicas: 1
      selector:
        deploymentconfig: ${NAME}
      template:
        metadata:
          labels:
            deploymentconfig: ${NAME}
        spec:
          containers:
          - name: apache-kafka
            image: ${IMAGE}
            command:
            - bin/kafka-server-start.sh
            args:
            - config/server.properties
            - --override
            - advertised.host.name=${NAME}
            - --override
            - log.segment.bytes=10485760
            - --override
            - log.retention.bytes=10485760
            volumeMounts:
            - mountPath: /tmp/kafka-logs
              name: kafka-logs
            ports:
            - containerPort: 9092
          - name: apache-zookeeper
            image: ${IMAGE}
            command:
            - bin/zookeeper-server-start.sh
            args:
            - config/zookeeper.properties
            volumeMounts:
            - mountPath: /tmp/zookeeper
              name: zookeeper
            ports:
            - containerPort: 2181
          volumes:
          - name: kafka-logs
            emptyDir: {}
          - name: zookeeper
            emptyDir: {}
  - apiVersion: v1
    kind: Service
    metadata:
      name: ${NAME}
    spec:
      ports:
      - name: kafka
        port: 9092
      - name: zookeeper
        port: 2181
      selector:
        deploymentconfig: ${NAME}

```

## Deploy the Apache Kafka + Apache Zookeeper pod

``` bash
oc new-app apache-kafka
# oc logs --follow apache-kafka
```