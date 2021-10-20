## Important Links and commands and steps for connecting to Dev GKE and Imply and sending test data to Kafka and druid

### Connect to GKE Dev cluster
* command: `gcloud container clusters get-credentials metacx-us-central1 --region us-central1 --project com-metacx-integration`

### imply manager port forward
* command: `kubectl --namespace imply port-forward svc/imply-integration-manager-int 9097`

### imply Query/Router
* command: `kubectl --namespace imply port-forward svc/imply-integration-query 8888 9095`

### imply overload port forward
* command: `curl http://localhost:8888/druid/indexer/v1/supervisor`

### Submitting a Supervisor Spec
* command: `curl -X POST -H 'Content-Type: application/json' -d @PATH_TO_INGESTION_SPECS http://localhost:8888/druid/indexer/v1/supervisor`

### Insert data from Kafka

#### Get Pod name for Kafka
* command: `export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")`

#### Executive kafka producer shell script
* command: `kubectl --namespace kafka exec -it $POD_NAME -- kafka-console-producer.sh --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092 --topic imply-test1  --producer.config /opt/bitnami/kafka/config/producer.properties`



Command:

curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Integration/Ingestion/signalPocIngestionSpec.json http://localhost:8888/druid/indexer/v1/supervisor

--> Insert data from Kafka

export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace kafka exec -it $POD_NAME -- kafka-console-producer.sh --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092,kafka-2.kafka-headless.kafka.svc.cluster.local:9092 --topic imply-test1  --producer.config /opt/bitnami/kafka/config/producer.properties

###### Loading data from Producer ##########

"eventId",
"signalId",
"rowId",
"companyId"


{"time":"2021-10-03T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"metacx1","num3":3,"num4":4,"arrValue":10}

----------------------------------------------------------------------

1. Dynamic Schema: Change the ingestion spec and re-submit the spec to supervisor..

curl -X GET -H 'Content-Type: application/json' http://localhost:8888/druid/indexer/v1/supervisor/imply-test1

curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/Deployment/Dev/ingestion/imply_first_kafkaIngestionSpec.json http://localhost:8888/druid/indexer/v1/supervisor/

{"time":"2021-10-03T01:02:33Z","eventId":"5","signalId":"s5","rowId":"r5","companyId":"metacx1","num3":3,"arrValue":10}

{"time":"2021-10-03T01:02:33Z","eventId":"5","signalId":"s5","rowId":"r5","companyId":"metacx1","num3":3,"num4":4,"arrValue":10}

2. Query the datasource through currently

curl -X GET -H 'Content-Type: application/json' http://localhost:8888/druid/v2/datasources/imply-test1

curl -X POST -H'Content-Type: application/json' http://localhost:8888/druid/v2/sql/ -d @/Users/sohan/Desktop/MetaCX/Imply/Deployment/Dev/ingestion/query.json

3. Re-indexing
curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/Deployment/Dev/ingestion/reingestion_ingestionSpecs.json  http://localhost:8888/druid/indexer/v1/task


------------------------------------------------

Re-indexing test:

1. Insert first new data for 10/01 Date

{"time":"2021-10-01T01:03:33Z","eventId":"100","signalId":"s100","rowId":"r100","companyId":"salesforce100","arrValue":100}

2. Run re-indexing task for the same timeframe: 10/01

curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/Deployment/Dev/ingestion/reingestion_ingestionSpecs.json  http://localhost:8888/druid/indexer/v1/task

3. Insert new data for same timeframe: 10/01

kubectl --namespace kafka exec -it $POD_NAME -- kafka-console-producer.sh --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092 --topic imply-test1  --producer.config /opt/bitnami/kafka/config/producer.properties


{"time":"2021-10-01T03:03:33Z","eventId":"600","signalId":"s600","rowId":"r600","companyId":"salesforce600","arrValue":600}
