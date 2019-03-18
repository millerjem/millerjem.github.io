---
layout: default
parent: Kubernetes Lab
title: Deploying Another Kubernetes Cluster
nav_order: 2
---

# Launch a second Kubernetes cluster

In this lab, you will launch a second Kubernetes cluster on the same DC/OS cluster, demonstrating Mesosphere Kubernetes Engine's ability to run high-density, multi-kubernetes environments.

### Step 1
Create SSL keys, Service Account, and Secret for Kubernetes Cluster 2

Run the commands below to create the SSL keys, service account, and secret for your second Kubernetes cluster.

```
dcos security org service-accounts keypair private-key.pem public-key.pem
dcos security org service-accounts create -p public-key.pem -d "Kubernetes cluster 2 service account" kubernetes-cluster2
dcos security secrets create-sa-secret private-key.pem kubernetes-cluster2 kubernetes-cluster2/sa
```

### Step 2
Add permissions

Run the commands below to grant the service account permissions to create and view Kubernetes clusters.

```
dcos security org groups add_user superusers kubernetes-cluster2
```

### Step 3
Launch the second Kubernetes cluster

Now, we will launch a Kubernetes cluster using the service account and secret we just created. Copy and paste the command below into your terminal to create a package installer options file that references the service account and secret we just created.

**Be sure to copy and paste the entire code snippet below (including the final line containing `EOF`)**

```
cat > cluster2-options.json << EOF
{
  "service": {
    "name": "kubernetes-cluster2",
    "service_account": "kubernetes-cluster2",
    "service_account_secret": "kubernetes-cluster2/sa"
  },
  "kubernetes": {
    "private_node_count": 2
  }
}
EOF
```

Then run the DC/OS Kubernetes CLI command to launch the Kubernetes cluster.

```
dcos kubernetes cluster create --options=cluster2-options.json --yes
```

Your new Kubernetes cluster will take a few minutes to spin up. You can see the installation runbook automation and monitor the status of installation of each component with the command below:

```
watch dcos kubernetes manager plan status deploy --name=kubernetes-cluster2
```

When all the Kubernetes cluster elements report a status of `COMPLETE`, your new cluster is ready.
