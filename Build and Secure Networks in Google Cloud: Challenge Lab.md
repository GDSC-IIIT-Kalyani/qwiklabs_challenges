# Build and Secure Networks in Google Cloud: Challenge Lab

> Launch the lab [here](https://google.qwiklabs.com/quests/128?utm_source=google&utm_medium=lp&utm_campaign=gcpskills)

## Your challenge

You need to configure this simple environment securely. Your first challenge is to set up appropriate firewall rules and virtual machine tags. You also need to ensure that SSH is only available to the bastion via IAP.

For the firewall rules, make sure:

- The bastion host does not have a public IP address.
- You can only SSH to the bastion and only via IAP.
- You can only SSH to juice-shop via the bastion.
- Only HTTP is open to the world for `juice-shop`.

Tips and tricks:

- Pay close attention to the network tags and the associated VPC firewall rules.
- Be specific and limit the size of the VPC firewall rule source ranges.
- Overly permissive permissions will not be marked correct.

## Solving tasks

### Task 1: Check the firewall rules. Remove the overly permissive rules.

* Go to **VPC network** > **Firewall** > will see **open-access**
* Use the following command from the cloud console:

```yaml
gcloud compute firewall-rules delete open-access
```

### Task 2: Navigate to Compute Engine in the Cloud Console and identify the bastion host. The instance should be stopped. Start the instance

* Go to **Compute Engine**  > **VM Instances** > Select **bastion** > click on **Start**

### Task 3: The bastion host is the one machine authorized to receive external SSH traffic. Create a firewall rule that allows SSH (tcp/22) from the IAP service. The firewall rule should be enabled on bastion via a network tag.

* Run the following:
* Make sure you replace <SSH IAP network tag> with the tag provided on the Left Pane.

```yaml
gcloud compute firewall-rules create <SSH IAP network tag> --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags <SSH IAP network tag> --network acme-vpc

gcloud compute instances add-tags bastion --tags=<SSH IAP network tag> --zone=us-central1-b
```

### Task 4: The `juice-shop` server serves HTTP traffic. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address. The firewall rule should be enabled on juice-shop via a network tag

- Run the following:
* Make sure you replace <HTTP network tag> with the tag provided on the Left Pane.

```yaml
gcloud compute firewall-rules create <HTTP network tag> --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags <HTTP network tag> --network acme-vpc

gcloud compute instances add-tags juice-shop --tags=<HTTP network tag> --zone=us-central1-b
```

### Task 5: You need to connect to `juice-shop` from the bastion using SSH. Create a firewall rule that allows traffic on SSH (tcp/22) from `acme-mgmt-subnet` network address. The firewall rule should be enabled on `juice-shop` via a network tag

* Run the following:
* Make sure you replace <SSH internal network tag> with the tag provided on the Left Pane.

```yaml
gcloud compute firewall-rules create <SSH internal network tag> --allow=tcp:22 --source-ranges 192.168.10.0/24 --target-tags <SSH internal network tag> --network acme-vpc

gcloud compute instances add-tags juice-shop --tags=<SSH internal network tag> --zone=us-central1-b
```

### Task 6: In the Compute Engine instances page, click the SSH button for the bastion host. Once connected, SSH to `juice-shop`

* Go to **Compute Engine** > **VM instances** > **SSH** to **bastion** host
* Run the following:

```yaml
ssh <internal IP of the juice-shop>
```

