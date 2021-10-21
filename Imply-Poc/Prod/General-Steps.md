## Important Links and commands and steps for connecting to Dev GKE and Imply and sending test data to Kafka and druid

### Connect to GKE Production cluster
* `gcloud container clusters get-credentials metacx-us-central1 --region us-central1 --project com-metacx-production`

## Check which environment you are connected to 
* `kubectl config view | grep current-context`

### imply manager port forward
* `kubectl --namespace imply port-forward svc/imply-production-manager9097`

### imply Query/Router
* `kubectl --namespace imply port-forward svc/imply-production-query 8888 9095`

### imply overload port forward
* `curl http://localhost:8888/druid/indexer/v1/supervisor`

### Submitting a Supervisor Spec
* `curl -X POST -H 'Content-Type: application/json' -d @{{PATH_TO_INGESTION_SPECS}} http://localhost:8888/druid/indexer/v1/supervisor`

* Example: 
`curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Prod/Ingestion/signalPocIngestionSpec.json http://localhost:8888/druid/indexer/v1/supervisor`

### Insert data to Kafka

#### Get Pod name for Kafka
* `export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")`

#### Executive kafka producer shell script to insert data 
* `kubectl --namespace kafka exec -it $POD_NAME -- kafka-console-producer.sh --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092,kafka-2.kafka-headless.kafka.svc.cluster.local:9092 --topic imply-test1  --producer.config /opt/bitnami/kafka/config/producer.properties`

* Sample data: 
`{"time":"2021-10-03T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"metacx1","num3":3,"num4":4,"arrValue":10}`
