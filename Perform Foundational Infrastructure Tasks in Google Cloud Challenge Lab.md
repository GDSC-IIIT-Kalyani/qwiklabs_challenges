# Perform Foundational Infrastructure Tasks in Google Cloud: Challenge Lab

> Launch the lab [here](https://google.qwiklabs.com/quests/118)

In this lab there is no command line instructions, you have to just use the **Google Cloud Platform (GCP)** for completing this lab. A walkthrough of each task is available below: 

## Your challenge

You are now asked to help a newly formed development team with some of their initial work on a new project around storing and organizing photographs, called memories. You have been asked to assist the memories team with initial configuration for their application development environment; you receive the following request to complete the following tasks:

- Create a bucket for storing the photographs.
- Create a Pub/Sub topic that will be used by a Cloud Function you create.
- Create a Cloud Function.
- Remove the previous cloud engineer’s access from the memories project.

Some Jooli Inc. standards you should follow:

- Create all resources in the **us-east1** region and **us-east1-b** zone, unless otherwise directed.
- Use the projectdatd VPCs.
- Naming is normally *team-resource*, e.g. an instance could be named **kraken-webserver1**
- Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share; unless directed, use **f1-micro** for small Linux VMs and **n1-standard-1** for Windows or other applications such as Kubernetes nodes.

## Solving tasks

### Task 1: Create a bucket

1. Navigation menu > **Cloud Storage** > Browser > Create Bucket
2. Name your bucket > Enter **GCP Project ID** > Continue
3. Choose where to store your data > **Region:** us-east1 > Continue
4. Use default for the remaining 
5. Create

### Task 2: Create a Pub/Sub topic

1. Navigation menu > **Pub/Sub** > Topics
2. Create Topic > **Name:** Jooli > Create Topic

### Task 3: Create the thumbnail Cloud Function

1. Navigation menu > **Cloud Functions** > Create Function

2. Use the following config:

   **Name:** CFJooli
   **Region:** us-east1
   **Trigger:** Cloud Storage
   **Event type:** Finalize/Create
   **Bucket:** BROWSE > Select the qwiklabs bucket

3. Remaining default > Next

4. **Runtime:** Node.js 10
   **Entry point:** thumbnail
5. Add the code appropiately
6. Download the image from URL
7. Navigation menu > **Cloud Storage** > Browser > Select your bucket > Upload files
8. Refresh bucket

### Task 4: Remove the previous cloud engineer

1. Navigation menu > **IAM & Admin** > IAM
2. Search for the "**Username 2**" > Edit > Delete Role
