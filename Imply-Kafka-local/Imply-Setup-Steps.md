## Below are the steps for installing and running Imply locally on Minikube

* The following steps take you through installing Imply on a single machine using Minikube and an Imply-maintained Helm chart. Minikube is a small-scale, single-node Kubernetes engine suitable for exploratory purposes.

* We'll set up a local cluster consisting of:

    * Imply Manager
    * An Imply cluster with one master node, one query node, and two data nodes
    * A MySQL server (metadata storage for both the manager and the Imply cluster)
    * A single-node ZooKeeper cluster
    * A single-node MinIO server (as deep storage)
    * The commands assume that you are using OSX, but can be easily adapted for a Windows or Linux OS. We will be using the Homebrew package manager to install Minikube and Helm. If you do not already have Homebrew, get it here: (https://brew.sh/).

### Step 1: Install Minikube and create a Kubernetes cluster
* If you already have a Kubernetes environment available to use, you can skip to the next step. Our Kubernetes cluster will use 3 CPUs, 6 GB RAM, and 16 GB disk space. Your target machine should be able to support those specifications, otherwise adjust the amount of resources requested.

* Install Minikube and HyperKit (a hypervisor for OSX) with the following command: 

    `brew install minikube hyperkit`

### Start a local Kubernetes cluster with the required resources:
* Adjust the resource from Docker desktop if you are falling short of memory.

    `minikube start --cpus 3 --memory 8192 --disk-size 24g --vm-driver=hyperkit`

 * THIS IS JUST FOR INFORMATIONAL PURPOSE. The 'hyperkit' driver requires elevated permissions. The following commands will be executed as a part of the above command execution. 
    ```sh
    $ sudo chown root:wheel /Users/sohan/.minikube/bin/docker-machine-driver-hyperkit 
    $ sudo chmod u+s /Users/sohan/.minikube/bin/docker-machine-driver-hyperkit
    ```
* This creates a new cluster named `minikube`. If you already have an existing Minikube profile with the same name, you can create a different cluster by adding `--profile [new-profile-name]` to the minikube start command and any subsequent minikube commands.

* Call `minikube profile list` to view your clusters.


### Step 2: Install and configure Helm
* Helm is a package manager for Kubernetes. Imply's Kubernetes implementation uses a Helm chart to handle setup and administration functions for the Imply cluster.
* If you already have Helm installed, ensure that it is v3 or later, and skip to the next step. Otherwise:
    * Install Helm with the following command:
        
        `brew install helm`

    * Add the Imply repository to Helm by running: 
        
        `helm repo add imply https://static.imply.io/onprem/helm`
        `helm repo update`

### Step 3: Apply your Imply Manager license
* By default, a new installation includes a 30-day trial license. If you have a longer-term license that you want to apply, follow these steps:

    * Create a file named `IMPLY_MANAGER_LICENSE_KEY` and paste your license key as the content of the file.
    * Create a Kubernetes secret named imply-secrets by running:
        `kubectl create secret generic imply-secrets --from-file=IMPLY_MANAGER_LICENSE_KEY`

    * Example: `kubectl create secret generic imply-secrets --from-file=/Users/sohan/Desktop/MetaCX/Imply/imply-poc/Deployment/Dev/IMPLY_MANAGER_LICENSE_KEY`

### Step 4: Install Imply
* Deploy the Imply chart to install Imply:
    
    `helm install imply imply/imply`

* The chart will take a few minutes to deploy, after which you will be presented with information on how to access your cluster.

```sh
NAME: imply
LAST DEPLOYED: Fri Oct 22 10:27:35 2021
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

* As noted in the Helm output, set up port forwarding to access the following services:

    * Imply Manager
    
    `kubectl --namespace default port-forward svc/imply-manager-int 9097`

    * Pivot and Druid console
    
    `kubectl --namespace default port-forward svc/imply-query 8888 9095`

### Step 5: Access Imply services

* Start your Imply cluster.

    * Access the Imply Manager UI in your web browser at (http://localhost:9097).
    * Create an account, or log into your account. You will be prompted to create an account the first time you access the Imply Manager.
    * A cluster is created for you by default. Start the cluster in Imply Manager by clicking Manage then Start.
    * Once the cluster is running, access the Druid and Pivot UIs from your web browser at the following addresses:

    * Druid console: (http://localhost:8888)
    * Pivot: http://localhost:9095
    
    * You will not be able to access Pivot or the Druid console until the cluster is running.

### Step 6: Stop, restart, or remove Imply on Minikube

* To terminate your Imply processes, stop your Minikube node by running the command:
    
    `minikube stop`

* When you stop Minikube, your user data is kept intact. To restart the cluster, call minikube start as shown below.

    `minikube start --cpus 3 --memory 8192 --disk-size 24g --vm-driver=hyperkit`

    * You will not need to reinstall Imply, but you will need to repeat the port forwarding steps. It may take a few minutes for Imply to restart.

* Finally, if you want to remove your existing installation of Imply, you can uninstall the application:

    `helm delete imply`

    * Or you can delete the Minikube cluster entirely:

    `minikube delete`
