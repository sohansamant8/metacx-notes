## Below are the steps for installing and running Imply locally on Minikube

### Install Minikube if not already done
* Install Minikube on your system according to the following link (https://minikube.sigs.k8s.io/docs/start/). If you are using Homebrew, run the following command:
    `brew install minikube`

### Start Minikube 
* Start a local Kubernetes cluster with the required resources. Adjust the resource from Docker desktop if you are falling short of memory.
    `minikube start --cpus 2 --memory 4000 --disk-size 12g`

### Install Helm if not already done.
* If you are using Homebrew, the command is:
    `brew install helm`

* The remaining steps assume that you are using Helm 3 or later.

### Add Imply repository
* Add the Imply repository to Helm by running: 

    `helm repo add imply https://static.imply.io/onprem/helm`
    `helm repo update`

### Create secret for license key
* With `Helm`, there are two ways to provide your license key to the Imply Manager. 
    * You can either supply it directly in Helm's values.yaml configuration file, or 
    * Create it as a K8s Secret and provide Helm with a reference to the secret name. This is more secure, and is the method we will be using here.

* Create the secret containing the license key:
    * Create a file named IMPLY_MANAGER_LICENSE_KEY and paste your license key as the content of the file.
    * Create a K8s secret named imply-secrets by running:
        `kubectl create secret generic imply-secrets --from-file={{PATH_TO_FILE}}/IMPLY_MANAGER_LICENSE_KEY`

### Install Imply Manager chart

* We are now ready to deploy our Imply chart. For this quickstart, we will install the default version of the chart, which will create:

    * a MySQL server (metadata storage for both the manager and the Imply cluster)
    * a single-node ZooKeeper cluster
    * a single-node MinIO server (as deep storage)
    * Imply Manager
    * An Imply cluster with:
        * 1 master node
        * 1 query node
        * 1 data node

* Install the chart by running:
    `helm install imply imply/imply`

```sh
NAME: imply
LAST DEPLOYED: Fri Oct 22 09:21:07 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
Release name: imply

It may take a few minutes for the deployment to become available.

Manager
-------
Once all the pods are running, you can access the Manager console at http://127.0.0.1:9097 by running:

  kubectl --namespace default port-forward svc/imply-manager-int 9097

Pivot and Druid Console
-----------------------
Once the Imply Manager reports that the cluster is running, you can set up port forwarding by running:

  kubectl --namespace default port-forward svc/imply-query 8888 9095

Then connect to:
  - Pivot: http://127.0.0.1:9095
  - Druid: http://127.0.0.1:8888
```