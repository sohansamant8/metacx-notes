## Steps for Kafka - Docker-Compose 

### Create the `Docker-compose.yaml` file as shared.

### Run the `Docker-compose.yaml`. This should spin up both `imply` and `kafka` containers.

`docker-compose up -d`

### Create Ingestion Specs and Update the ingestion spec to use Kafka Consumer Properties - bootstrap server details as below: 

```sh
"bootstrap.servers": "kafka:9092"
```

### Post the ingestion specs to imply overload process

`curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Integration/Ingestion/imply_first_kafkaIngestionSpec.json http://localhost:8090/druid/indexer/v1/supervisor`

### Command to send test data to Kafka 

```sh
docker exec -it kafka /opt/bitnami/kafka/bin/kafka-console-producer.sh \
--broker-list kafka:9092 \
--topic imply-test1 \
--producer.config /opt/bitnami/kafka/config/producer.properties
```

### Sample data
`{"time":"2021-10-03T01:02:33Z","eventId":"5","signalId":"s5","rowId":"r5","companyId":"metacx1","num3":3,"num4":4,"arrValue":10}`

`{"time":"2021-10-03T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"metacx2","num3":2,"num4":4,"arrValue":20}`

`{"time":"2021-10-03T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"salesforce","num3":2,"num4":4,"arrValue":20}`

{"time":"2021-10-10T01:02:33Z","eventId":"1","signalId":"s1","rowId":"r1","companyId":"servicenow1","num3":2,"num4":4,"arrValue":20}

### Run Imply image only

`docker run --rm -p 9095:9095 -p 8081:8081 -p 8090:8090 -p 8888:8888 -p 8082:8082 -p 8083:8083 -p 8091:8091 imply-local`