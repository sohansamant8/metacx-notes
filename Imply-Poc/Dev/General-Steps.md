
## Important Links and commands and steps for connecting to Dev GKE and Imply and sending test data to Kafka and druid

### Connect to GKE Dev cluster
* `gcloud container clusters get-credentials metacx-us-central1 --region us-central1 --project com-metacx-dev`

### imply manager port forward
* `kubectl --namespace imply port-forward svc/implydev-manager-int 9097`

### imply Query/Router port forward
* `kubectl --namespace imply port-forward svc/implydev-query 8888 9095`

### imply overload port forward
* `curl http://localhost:8888/druid/indexer/v1/supervisor`

### Submitting a Supervisor Spec
* `curl -X POST -H 'Content-Type: application/json' -d @PATH_TO_INGESTION_SPECS http://localhost:8888/druid/indexer/v1/supervisor`

### Insert data from Kafka

#### Get Pod name for Kafka
* `export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")`

#### Executive kafka producer shell script
* `kubectl --namespace kafka exec -it $POD_NAME -- kafka-console-producer.sh --broker-list kafka-0.kafka-headless.kafka.svc.cluster.local:9092,kafka-1.kafka-headless.kafka.svc.cluster.local:9092 --topic imply-test1  --producer.config /opt/bitnami/kafka/config/producer.properties`

#### Loading data from Producer

```yaml
 {"time":"2021-10-01T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"salesforce2","arrValue":10}
 {"time":"2021-10-02T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"salesforce","arrValue":20}
 {"time":"2021-10-01T01:02:33Z","eventId":"2","signalId":"s2","rowId":"r2","companyId":"serviceNow","arrValue":10}
 {"time":"2021-10-01T01:02:33Z","eventId":"3","signalId":"s3","rowId":"r3","companyId":"BestBuy","arrValue":10}
 ```
