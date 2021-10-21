### Imply-POC Sending Signals to Druid in Dev and running app locally

## Below are the steps for creating Ingestion Spec for sending Signals to Druid

## Kafka Details 
* Kafka can be accessed by consumers via port 9092 on the following DNS name from within your cluster:

    ```yaml
    kafka.kafka.svc.cluster.local
    ```

* Each Kafka broker can be accessed by producers via port 9092 on the following DNS name(s) from within your cluster. Below brokers are for integration environment. In Dev environment, we have Kafka-0 and Kafka-1 only.

    ```yaml
    kafka-0.kafka-headless.kafka.svc.cluster.local:9092
    kafka-1.kafka-headless.kafka.svc.cluster.local:9092
    kafka-2.kafka-headless.kafka.svc.cluster.local:9092
    ```

### imply manager port forward
* `kubectl --namespace imply port-forward svc/implydev-manager-int 9097`

### imply Query/Router port forward
* `kubectl --namespace imply port-forward svc/implydev-query 8888 9095`

### imply overload port forward
* `curl http://localhost:8888/druid/indexer/v1/supervisor`

### Submit a Supervisor Spec. This will create Ingestion pipeline which will connect kafka topic and Druid data source. 
* `curl -X POST -H 'Content-Type: application/json' -d @{{PATH_TO_INGESTION_SPECS}} http://localhost:8888/druid/indexer/v1/supervisor`

* Example Command given below: 
`curl -X POST -H 'Content-Type: application/json' -d @/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Integration/Ingestion/signalPocIngestionSpec.json http://localhost:8888/druid/indexer/v1/supervisor`

### Verify if ingestion pipeline is created or not.
* Open Browser: (http://localhost:8888)

## To test the app on dev environment, please `deploy and build` the app as given below in Deployment steps. Once the app is deployed in Dev environment, the signals will start flowing in and you should see data getting loaded in Druid.

## To run the app locally and send a signal locally, follow below steps: 

### Getting kafka pod name 
* `export POD_NAME=$(kubectl get pods --namespace kafka -l "app.kubernetes.io/name=kafka,app.kubernetes.io/instance=kafka,app.kubernetes.io/component=kafka" -o jsonpath="{.items[0].metadata.name}")`

### If you are running the app locally do port forward in different terminal windows/tab for both the kafka pods: kafka-0 and Kafka-1 
* `kubectl --namespace kafka port-forward --address 127.0.0.1 pod/kafka-0 9092`
* `kubectl --namespace kafka port-forward --address 127.0.0.2 pod/kafka-1 9092`

### Enable the `socket handler` code in `publishToKafka.js` file

### Start all the application services. Through terminal navigate to Jupiter code location and execute the following steps:
* Run bootstap.
`npm run bootstrap`
* Start the Databases.
`npm run db:start`
* Launch API services and cloud function emulators (wait for this to settle).
`npm run dev:api` 
* Initialize database. Open another terminal window and this uses API. Note down the user ids and passwords that you get as the seeding completes.
`npm run db:seed`
* Launch the application. This should open a new browser window.
`npm run dev:web`

### Login to MetaCX application which is opened through `dev:web`

### Open the web app and create new connection as below.
* Go to CX-Reactor. 
* On the left top, click on Connections.
* On the right bottom, create new connection using `add connection`. 
* Give connection name.
    * Select `Generate Tracking Script` on save because we dont want to go through authentication while sending the signal.
    * save and proceed. 
* Refresh the page to see the connection created and then click on that connection.
* Once connection pop-up opens up, click on `show tracking script`
* Note down and save the `Analytics Key` as that is the connectionId which will be used to send in the events.

### In the terminal send one event and check if it appears on the web-app. Change the `connectionId` in the below `curl` command.

`curl -d '{"eventKey": "Login-report","connectionId": "c659ca6072b67978c9e6d616","data": [{"eventDate": 1634321717375,"companyId": "metacx","LoginCount": 12,"myStringField1": "salesforce","myStringField2": "accountLogins"}]}' -H "Content-Type: application/json" -X POST 'http://localhost:4007/analytics'`

### Sample Event which is used above.
```yaml
{
    "eventDate": 1634321717375,
    "companyId": "metacx",
    "LoginCount": 12,
    "myStringField1": "salesforce",
    "myStringField2": "accountLogins"
}
```

### Check the Druid datasource to verify if the data is ingested.
* Open Browser: (http://localhost:8888)
