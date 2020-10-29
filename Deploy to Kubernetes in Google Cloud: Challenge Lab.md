# Deploy to Kubernetes in Google Cloud: Challenge Lab

> Launch the lab [here](https://google.qwiklabs.com/focuses/10457?parent=catalog)

## Your challenge

You are expected to create container images, store the images in a repository, and configure a Jenkins CI/CD pipeline to automate the build for the product. Your know that Kurt, your supervisor, will ask you to complete these tasks:

- Create a Docker image and store the Dockerfile.
- Test the created Docker image.
- Push the Docker image into the Container Repository.
- Use the image to create and expose a deployment in Kubernetes
- Update the image and push a change to the deployment.
- Create a pipeline in Jenkins to deploy a new version of your image when the source code changes.

Some Jooli Inc. standards you should follow:

- Create all resources in the `us-east1` region and `us-east1-b` zone, unless otherwise directed.
- Use the project VPCs.
- Naming is normally *team-resource*, e.g. an instance could be named **kraken-webserver1**.
- Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share: unless directed, use `n1-standard-1`.

### Task 1: Create a Docker image and store the Dockerfile

```yaml
gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh | bash
gcloud source repos clone valkyrie-app
cd valkyrie-app
cat > Dockerfile <<EOF
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
EOF
docker build -t valkyrie-app:v0.0.1 .
cd ..
cd marking
./step1.sh
```

### Task 2: Test the created Docker image

```yaml
cd ..
cd valkyrie-app
docker run -p 8080:8080 valkyrie-app:v0.0.1 &
cd ..
cd marking
./step2.sh
```

### Task 3: Push the Docker image in the Container Repository

```yaml
cd ..
cd valkyrie-app
docker tag valkyrie-app:v0.0.1 gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1
docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1
sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml
```

### Task 4: Create and expose a deployment in Kubernetes

```yaml
sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml
gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d
kubectl create -f k8s/deployment.yaml
kubectl create -f k8s/service.yaml

git merge origin/kurt-dev
kubectl edit deployment valkyrie-dev
```

### Task 5: Update the deployment with a new version of valkyrie-app

```yaml
docker build -t gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2 . 
docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2
kubectl edit deployment valkyrie-dev

docker ps
```

### Task 6: Create a pipeline in Jenkins to deploy your app

```yaml
docker kill container_id

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

gcloud source repos list

sed -i "s/green/orange/g" source/html.go

# Update project in Jenkinsfile

sed -i "s/YOUR_PROJECT/$GOOGLE_CLOUD_PROJECT/g" Jenkinsfile
git config --global user.email "you@example.com"     <------ put from first consol 
git config --global user.name "student"               <--------- from login status
git add .
git commit -m "built pipeline init"
git push 
```

* Now, open the Jenkins window and run the **Build Pipeline**.

