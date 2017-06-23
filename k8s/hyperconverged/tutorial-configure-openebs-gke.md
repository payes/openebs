# Using OpenEBS with Kubernetes on Google Container Engine

This tutorial, provides detailed instructions on how to setup and use OpenEBS in Google Container Enginer (GKE). This tutorial uses a 3 node container cluster. 

## Step 1 : Preparing your Container Cluster 

You can either use an existing container cluster or create a new one using, Login to your Google Cloud Platoform -> Container Engine -> Create Container Cluster. The minimum requirements for the cluster are as follows:

### Minimum Requirements
- Machine Type - (Minimum 2 vCPUs)
- Node Image - (container-vm)
- Size - (Minimum 3)
- Cluster Version - (1.6.4+)

### Add iSCSI Support

SSH into the nodes of the cluster to install open-iscsi package. OpenEBS 0.3 uses iscsi to connect to the block volumes. 

```
sudo apt-get update
sudo apt-get install open-iscsi
sudo service open-iscsi restart
```

#### Verify that iSCSI is configured

Check that initiator name is configured and iscsi service is running
```
sudo cat /etc/iscsi/initiatorname.iscsi
sudo service open-iscsi status
```

## Step 2 : Run OpenEBS Operator

Download the latest OpenEBS Operator Files
```
git clone https://github.com/openebs/openebs.git
cd openebs/k8s
```

Setup the kubectl to run in admin context. (See Appendix below for creating an admin context in GCP). The below command will prompt for username/password. Provide username as "admin" and password for the admin can be obtained from : Google Cloud Platform -> Container Engine -> (cluster) -> Show Credentials
```
kubectl config use-context demo-openebs03
kubectl apply -f openebs-operator.yaml
kubectl config use-context gke_strong-eon-153112_us-central1-a_demo-openebs03
```

## Step 3 : Running Stateful workloads with OpenEBS Storage. 


## Appendix

### Setting kubernetes cluster admin context

To create/modify service accounts and grant previleges, kubectl needs to be run with admin previleges. The following commands help to setup and use the admin context for Google Container Engine via the Google Cloud Shell. 

#### Step 1: Initialize credentails to allow kubectl to execute commands on the container cluster. 
```
gcloud container clusters list
gcloud container clusters get-credentials demo-openebs03 --zone us-central1-a
```

#### Step 2 : Setup the admin context

- Access the credentails information from : Google Cloud Platform -> Container Engine -> (cluster) -> Show Credentials. 
- Save the "Cluster CA Certificate" to ~/.kube/admin.key
- Create a admin config context from the config shell
```
gcloud container clusters list
kubectl config set-context demo-openebs03 --cluster=gke_strong-eon-153112_us-central1-a_demo-openebs03 --user=cluster-admin
```






