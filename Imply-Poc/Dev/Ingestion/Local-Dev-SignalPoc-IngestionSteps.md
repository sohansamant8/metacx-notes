### Imply-POC Sending Signals to Druid in Dev and running app locally

## Below are the steps for creating Ingestion Spec for sending Signals to Druid

## Kafka Details 
* Kafka can be accessed by consumers via port 9092 on the following DNS name from within your cluster:

    `kafka.kafka.svc.cluster.local`

* Each Kafka broker can be accessed by producers via port 9092 on the following DNS name(s) from within your cluster. Below brokers are for integration environment. In Dev environment, we have just Kafka-0 and Kafka-1

    `kafka-0.kafka-headless.kafka.svc.cluster.local:9092`
    `kafka-1.kafka-headless.kafka.svc.cluster.local:9092`
    `kafka-2.kafka-headless.kafka.svc.cluster.local:9092`

* To create a pod that you can use as a Kafka client run the following commands:
     `kubectl run kafka-client --restart='Never' --image docker.io/bitnami/kafka:2.8.1-debian-10-r0 --namespace kafka --command -- sleep infinity`
     `kubectl exec --tty -i kafka-client --namespace kafka -- bash`

## Submit the ingestion specs to Supervisor
* command: `curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Integration/Ingestion/signalPocIngestionSpec.json http://localhost:8888/druid/indexer/v1/supervisor`

## If you are running the app locally then port-forward must be done for localHost 

## Getting kafka pod name 
* command: `export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")`

## If you are running the app Locally do port forward for both the kafka pods: kafka-0 and Kafka-1 in different terminal tabs
* command: 
    * `kubectl --namespace kafka port-forward --address 127.0.0.1 pod/kafka-0 9092`
    * `kubectl --namespace kafka port-forward --address 127.0.0.2 pod/kafka-1 9092`
