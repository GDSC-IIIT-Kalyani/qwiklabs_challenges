# Perform Foundational Data, ML, and AI Tasks in Google Cloud: Challenge Lab

### Task 1:

* Navigation menu > Storage > Browser

* Create a **Storage** Bucket > Enter name as your GCP Project ID > Leave others to default > Create

* Go to **BigQuery** > Select project ID > Create Dataset > Enter the name as `lab` and click on Create

* Run the following from the Cloud Shell:

```yaml
gsutil cp gs://cloud-training/gsp323/lab.csv .

cat lab.csv

gsutil cp gs://cloud-training/gsp323/lab.schema .

cat lab.schema
```

* Now, create a table inside the `lab` dataset and configure it as follows:

![](https://github.com/DSC-IIIT-Kalyani/qwiklabs_challenges/raw/master/screenshots/table_1.png)

* Click on **Create table**

* Go to **Dataflow** > Jobs > Create Job from Template

![](https://github.com/DSC-IIIT-Kalyani/qwiklabs_challenges/raw/master/screenshots/job_1.png)

![](https://github.com/DSC-IIIT-Kalyani/qwiklabs_challenges/raw/master/screenshots/job_2.png)

* Run the Job.

### Task 2:

* Go to **Dataproc** > Clusters > Create Cluster

![](https://github.com/DSC-IIIT-Kalyani/qwiklabs_challenges/raw/master/screenshots/cluster.png)

* Select the Created Cluster > Go to **VM Instances** > **SSH** into cluster

* Run the following command: 

```yaml
hdfs dfs -cp gs://cloud-training/gsp323/data.txt /data.txt
```

* Exit the **SSH**

* Submit Job > Configure as given:

![](https://github.com/DSC-IIIT-Kalyani/qwiklabs_challenges/raw/master/screenshots/job_3.png)

* Click on **SUBMIT**

### Task 3:

* Go to **Dataprep** > Accept the terms > Login with the same account

* Import Data > Select GCS > Edit > Enter the path as this: `gs://cloud-training/gsp323/runs.csv` > Import and Wrangle

* Modify the table as specified in the lab instructions.

### Task 4:

PART 1

Use the following commands:

```yaml
gcloud iam service-accounts create my-natlang-sa \
  --display-name "my natural language service account"

gcloud iam service-accounts keys create ~/key.json \
  --iam-account my-natlang-sa@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com

export GOOGLE_APPLICATION_CREDENTIALS="/home/$USER/key.json"

gcloud auth activate-service-account my-natlang-sa@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com --key-file=$GOOGLE_APPLICATION_CREDENTIALS

gcloud ml language analyze-entities --content="Old Norse texts portray Odin as one-eyed and long-bearded, frequently wielding a spear named Gungnir and wearing a cloak and a broad hat." > result.json

gcloud auth login 
(Copy the token from the link provided)

gsutil cp result.json gs://YOUR_PROJECT-marking/task4-cnl.result
```

PART 2

* Create an API Key by going to IAM > Credentials, and export it as `API_KEY` variable in the Cloud Shell. 

* Create the following JSON file:

```yaml
nano request.json

{
  "config": {
      "encoding":"FLAC",
      "languageCode": "en-US"
  },
  "audio": {
      "uri":"gs://cloud-training/gsp323/task4.flac"
  }
}
```

* Run the following:

  > Replace `YOUR_PROJECT` with your GCP Project ID.

```yaml
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > result.json

gsutil cp result.json gs://YOUR_PROJECT-marking/task4-gcs.result
```

PART 3

* Run the following:

```yaml
gcloud iam service-accounts create quickstart

gcloud iam service-accounts keys create key.json --iam-account quickstart@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com

gcloud auth activate-service-account --key-file key.json

export ACCESS_TOKEN=$(gcloud auth print-access-token)
```

* Modify the previous JSON file:

```yaml
nano request.json

{
   "inputUri":"gs://spls/gsp154/video/chicago.mp4",
   "features": [
       "TEXT_DETECTION"
   ]
}
```

* Run the following:

> Replace `YOUR_PROJECT` with your GCP Project ID.

```yaml
curl -s -H 'Content-Type: application/json' \
    -H "Authorization: Bearer $ACCESS_TOKEN" \
    'https://videointelligence.googleapis.com/v1/videos:annotate' \
    -d @request.json



curl -s -H 'Content-Type: application/json' -H "Authorization: Bearer $ACCESS_TOKEN" 'https://videointelligence.googleapis.com/v1/operations/OPERATION_FROM_PREVIOUS_REQUEST' > result1.json


gsutil cp result1.json gs://YOUR_PROJECT-marking/task4-gvi.result
```



