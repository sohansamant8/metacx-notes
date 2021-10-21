
## Below are the common Kubectl commands that are used most often

### Connect to GKE Dev cluster
* `gcloud container clusters get-credentials metacx-us-central1 --region us-central1 --project com-metacx-dev`

### Connect to GKE Integration cluster
* `gcloud container clusters get-credentials metacx-us-central1 --region us-central1 --project com-metacx-integration`

### Connect to GKE Production cluster
* `gcloud container clusters get-credentials metacx-us-central1 --region us-central1 --project com-metacx-production`

### Export project or cluster 
* `export CLOUDSDK_CORE_PROJECT=com-metacx-integration`

### change the cluster to Integration
* `gcloud container clusters get-credentials metacx-us-central1`

### Copy the cluster name
* `kubectl config use-context <COPIED_CLUSTER_NAME>`

### Check which cluster you have connected to 
* `kubeclt config view | grep current-context`