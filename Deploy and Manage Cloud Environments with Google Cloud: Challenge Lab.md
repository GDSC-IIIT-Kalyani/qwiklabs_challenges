# Deploy and Manage Cloud Environments with Google Cloud: Challenge Lab

> Launch the lab [here](https://google.qwiklabs.com/quests/121?utm_source=google&utm_medium=lp&utm_campaign=gcpskills)

## Your challenge

As soon as you sit down at your desk and open your new laptop you receive the following request to complete these tasks.

> Do not wait for the lab to provision! You can complete tasks 1 and 2 before the lab provisioning has ended, just ensure the jumphost exists.

### Task 1: Create the production environment

* Go to **Compute Engine** > **VM Instances** >  **kraken-jumphost** > **SSH**
* Run the following there:

```yaml
cd /work/dm

sed -i s/SET_REGION/us-east1/g prod-network.yaml

gcloud deployment-manager deployments create prod-network --config=prod-network.yaml

gcloud config set compute/zone us-east1-b

gcloud container clusters create kraken-prod \
          --num-nodes 2 \
          --network kraken-prod-vpc \
          --subnetwork kraken-prod-subnet
          
gcloud container clusters get-credentials kraken-prod

cd /work/k8s
        
for F in $(ls *.yaml); do kubectl create -f $F; done
```

### Task 2: Setup the Admin instance

* Run the following from the **Cloud Terminal**:

```yaml
gcloud config set compute/zone us-east1-b

gcloud compute instances create kraken-admin --network-interface="subnet=kraken-mgmt-subnet" --network-interface="subnet=kraken-prod-subnet"

```

* Go to **Monitoring** > **Alerting** > **Create Alerting Policy**

* Add Condition (use the **kraken-admin instance ID**)

* **Manage notification Channel** > Create a new **Email Channel** 

* Click **Done**

### Task 3: Verify the Spinnaker deployment

* Run the following from the **Cloud Terminal**:

```yaml
gcloud config set compute/zone us-east1-b

gcloud container clusters get-credentials spinnaker-tutorial

DECK_POD=$(kubectl get pods --namespace default -l "cluster=spin-deck" -o jsonpath="{.items[0].metadata.name}")

kubectl port-forward --namespace default $DECK_POD 8080:9000 >> /dev/null &
```

* **Web Preview** > **Preview on port 8080**

* Go to **Applications** > **sample**

* Select **Pipelines**

* Start **Manual Execution** > Pipeline: **Deploy** > **Run**

* Click and continue the **PUBSUB**

* While the **MANUAL START** is running, execute the following:

```yaml
gcloud config set compute/zone us-east1-b

gcloud source repos clone sample-app

cd sample-app

touch a

git config --global user.email "$(gcloud config get-value account)"

git config --global user.name "Student"

git commit -a -m "change"

git tag v1.0.1

git push --tags
```

* Now, return to the **Spinnaker** page and allow the "**Continue to Deploy?**" message.

* Again, start another **MANUAL START** deployment (this time it will use the new changes)

* Wait the deployment to finish.