## Below are the steps for installing and running Kafka locally on Minikube

### Create new namespace for Kafka in the minikube

`kubectl create namespace kafka`

### First, add the Bitnami charts repository to Helm:

`helm repo add bitnami https://charts.bitnami.com/bitnami`

###  Install Zookeeper via Helm

```sh
helm install zookeeper bitnami/zookeeper \
  --set replicaCount=1 \
  --set auth.enabled=false \
  --set allowAnonymousLogin=true \
  -n kafka
```

* Sample output after the installation of Zookeeper: 

```sh
NAME: zookeeper
LAST DEPLOYED: Fri Oct 22 13:20:21 2021
NAMESPACE: kafka
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

ZooKeeper can be accessed via port 2181 on the following DNS name from within your cluster:

    zookeeper.kafka.svc.cluster.local

To connect to your ZooKeeper server run the following commands:

    export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=zookeeper,app.kubernetes.io/instance=zookeeper,app.kubernetes.io/component=zookeeper" -o jsonpath="{.items[0].metadata.name}")
    kubectl exec -it $POD_NAME -- zkCli.sh

To connect to your ZooKeeper server from outside the cluster execute the following commands:

    kubectl port-forward --namespace kafka svc/zookeeper 2181:2181 &
    zkCli.sh 127.0.0.1:2181
```

* Wait for a few minutes until the chart is deployed and note the service name displayed in the output, as you will need this in subsequent steps.

* Note: Record the output of this command for `Zookeeper Service Name`. In the above output it is: `zookeeper.kafka.svc.cluster.local`

### Install Kafka Via Helm
* Change replica counts in the following command as needed.
* Replace `{{ZOOKEEPER-SERVICE-NAME}}` with result from Zookeeper installation.

```sh
helm install kafka bitnami/kafka \
  --set zookeeper.enabled=false \
  --set replicaCount=2 \
  --set externalZookeeper.servers=zookeeper.kafka.svc.cluster.local \
  -n kafka
```
* This command will deploy a three-node Apache Kafka cluster and configure the nodes to connect to the Apache Zookeeper service. 
* Wait for a few minutes until the chart is deployed and note the service name displayed in the output, as you will need this in the next step.

### Sample Kafka installation output. You will be the Broker details and bootstap servers, hence save them for your further use. 
```sh
NAME: kafka
LAST DEPLOYED: Fri Oct 22 13:22:57 2021
NAMESPACE: kafka
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

Kafka can be accessed by consumers via port 9092 on the following DNS name from within your cluster:

    kafka.kafka.svc.cluster.local

Each Kafka broker can be accessed by producers via port 9092 on the following DNS name(s) from within your cluster:

    kafka-0.kafka-headless.kafka.svc.cluster.local:9092
    kafka-1.kafka-headless.kafka.svc.cluster.local:9092

To create a pod that you can use as a Kafka client run the following commands:

    kubectl run kafka-client --restart='Never' --image docker.io/bitnami/kafka:2.8.1-debian-10-r25 --namespace kafka --command -- sleep infinity
    kubectl exec --tty -i kafka-client --namespace kafka -- bash

    PRODUCER:
        kafka-console-producer.sh \
            --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092 \
            --topic test

    CONSUMER:
        kafka-console-consumer.sh \
            --bootstrap-server kafka.kafka.svc.cluster.local:9092 \
            --topic test \
            --from-beginning
```

## To validate kafka-Druid connection, Follow below steps:

### Do port-forward for imply-query server 

### Check if supervisor is up and running

`curl http://localhost:8888/druid/indexer/v1/supervisor`

### Submitting a Ingestion spec to Supervisor

`curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Dev/ingestion/imply_first_kafkaIngestionSpec.json http://localhost:8888/druid/indexer/v1/supervisor` 

### Insert data from Kafka
* Get Pod name for Kafka

`export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")`

* Executive kafka producer shell script

`kubectl --namespace kafka exec -it $POD_NAME -- kafka-console-producer.sh --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092 --topic imply-test1 --producer.config /opt/bitnami/kafka/config/producer.properties`

* Load data from Producer

```sh
 {"time":"2021-10-01T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"salesforce2","num3":10,"num4":20,"arrValue":10}
 {"time":"2021-10-02T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"salesforce","num3":10,"num4":20,"arrValue":20}
 {"time":"2021-10-01T01:02:33Z","eventId":"2","signalId":"s2","rowId":"r2","companyId":"serviceNow","num3":10,"num4":20,"arrValue":10}
 {"time":"2021-10-01T01:02:33Z","eventId":"3","signalId":"s3","rowId":"r3","companyId":"BestBuy","num3":10,"num4":20,"arrValue":10}
```