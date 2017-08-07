# Quick Start Explained ( part 1 of 4)
## Load resources (Templates, DeploymentConfigs, Services)
```bash
oc create -f https://raw.githubusercontent.com/rondinif/openshift-kafka/master/resources.yaml
```

## Deploy the Apache Kafka + Apache Zookeeper pod

``` bash
oc new-app apache-kafka
# oc logs --follow apache-kafka
```