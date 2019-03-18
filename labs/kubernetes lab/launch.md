---
title: Launch a Kubernetes Cluster
layout: default
parent: Kubernetes Lab
nav_order: 0
---

# Launch a Kubernetes  Cluster

### Step 1

To launch a Kubernetes cluster, you must first deploy the Mesosphere Kubernetes Engine (MKE) control plane manager. Your instructors have pre-installed MKE on your behalf, however you still need the DC/OS Kubernetes CLI package.

Install the DC/OS Kubernetes CLI package with the following command:

```
dcos package install kubernetes --yes --cli
```

You're now ready to launch your first Kubernetes cluster!

### Step 2

Now that the Kubernetes control plan manager is running, you can use it to launch a Kubernetes cluster.  Since you are using the Enterprise version of DC/OS, you can use the DC/OS certificate authority to create an SSL key to be used with a DC/OS service account user.

First, you must install the DC/OS Enterprise CLI, which gives you access to DC/OS Enterprise security features, among other useful tools.

```
dcos package install dcos-enterprise-cli --yes
```

Next, run the following commands to create the SSL keys, the service account and the secret.

```
dcos security org service-accounts keypair private-key.pem public-key.pem
dcos security org service-accounts create -p public-key.pem -d "Kubernetes cluster 1 service account" kubernetes-cluster1
dcos security secrets create-sa-secret private-key.pem kubernetes-cluster1 kubernetes-cluster1/sa
```

Once completed, you should be able to see the secret by clicking on the `Secrets` tab in the left navigation pane.

![secrets](https://raw.githubusercontent.com/tbaums/dcos-mandt-labs/master/screenshots/dcos-view-secrets.png)

You should also grant the correct permissions to allow the new service account to launch and view Kubernetes clusters. Run the following ACL commands:

```
dcos security org groups add_user superusers kubernetes-cluster1
```
*Note: See below for an example of more granular security settings that more closely resembles what you would use in production ([link](#production-permissions)).*

Now, we will launch a Kubernetes cluster using the service account and secret we just created. Copy and paste the command below into your terminal to create a package installer options file that references the service account and secret we just created.

**Be sure to copy and paste the entire code snippet below (including the final line containing `EOF`)**

```
cat > cluster1-options.json << EOF
{
  "service": {
    "name": "kubernetes-cluster1",
    "service_account": "kubernetes-cluster1",
    "service_account_secret": "kubernetes-cluster1/sa"
  },
  "kubernetes": {
    "public_node_count": 1,
    "private_node_count": 1,
    "high_availability": true
  }
}
EOF
```

Then run the DC/OS Kubernetes CLI command to launch the Kubernetes cluster.

```
dcos kubernetes cluster create --options=cluster1-options.json --yes
```

```
By Deploying, you agree to the Terms and Conditions https://mesosphere.com/catalog-terms-conditions/#certified-services
Kubernetes on DC/OS.

	Documentation: https://docs.mesosphere.com/service-docs/kubernetes-cluster
	Issues: https://github.com/mesosphere/dcos-kubernetes-quickstart/issues

Creating Kubernetes cluster kubernetes-cluster1
DC/OS Kubernetes is being installed!
Kubernetes cluster '[kubernetes-cluster1]' is being created
```

Your new Kubernetes cluster will take a few minutes to spin up. You can see the installation runbook automation and monitor the status of installation of each component with the command below:

```
watch dcos kubernetes manager plan status deploy --name=kubernetes-cluster1
```
First, it will show some Kubernetes components completed, and some started or pending like this:

```
$ watch dcos kubernetes manager plan status deploy --name=kubernetes-cluster1

deploy (serial strategy) (PENDING)
  etcd (serial strategy) (PENDING)
     etcd-0:[peer] (PENDING)
     etcd-1:[peer] (PENDING)
     etcd-2:[peer] (PENDING)
  control-plane (dependency strategy) (PENDING)
     kube-control-plane-0:[instance] (PENDING)
     kube-control-plane-1:[instance] (PENDING)
     kube-control-plane-2:[instance] (PENDING)
  mandatory-addons (serial strategy) (PENDING)
     mandatory-addons-0:[instance] (PENDING)
  node (dependency strategy) (PENDING)
     kube-node-0:[kubelet] (PENDING)
  public-node (dependency strategy) (PENDING)
     kube-node-public-0:[kubelet] (PENDING)
```

When it is completely installed, the plan status should look like this:

```
$ watch dcos kubernetes manager plan status deploy --name=kubernetes-cluster1

deploy (serial strategy) (COMPLETE)
  etcd (serial strategy) (COMPLETE)
     etcd-0:[peer] (COMPLETE)
     etcd-1:[peer] (COMPLETE)
     etcd-2:[peer] (COMPLETE)
  control-plane (dependency strategy) (COMPLETE)
     kube-control-plane-0:[instance] (COMPLETE)
     kube-control-plane-1:[instance] (COMPLETE)
     kube-control-plane-2:[instance] (COMPLETE)
  mandatory-addons (serial strategy) (COMPLETE)
     mandatory-addons-0:[instance] (COMPLETE)
  node (dependency strategy) (COMPLETE)
     kube-node-0:[kubelet] (COMPLETE)
  public-node (dependency strategy) (COMPLETE)
     kube-node-public-0:[kubelet] (COMPLETE)

```

### Step 3

Install Kubernetes `kubectl` Command Line

**For Windows** with `curl` installed the commands are:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/windows/amd64/kubectl.exe
```

Then, add the binary to your PATH.

**For Macs** with `brew` installed the command is:

```
brew install kubectl
```

**For CoreOS** the commands are:
```
curl -O curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mkdir -p /opt/bin
sudo mv kubectl /opt/bin/kubectl
```

**For Red Red or CentOS** the commands are:
```
curl -O curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mkdir -p /usr/local/bin
sudo mv kubectl /usr/local/bin/kubectl
```

**For Ubuntu** the commands are:

```
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo touch /etc/apt/sources.list.d/kubernetes.list
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

If your OS is not listed above, or if you need additional help installing kubectl please see [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

Confirm that kubectl is installed and in path `/usr/local/bin` using the command below. (Kubectl will say it is not connected to a dcos cluster yet, which is expected.)

```
kubectl version
```
-----------
## Production Permissions

Below are a set of example permissions for a Kubernetes cluster on DC/OS that more closely resemble what would be used in a production environment. They are provided for illustrative purposes only, and are not required for this lab exercise.

```
dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:task:user:root create
dcos security org users grant kubernetes-cluster dcos:mesos:agent:task:user:root create

dcos security org users grant kubernetes-cluster dcos:mesos:master:reservation:role:kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:reservation:principal:kubernetes-cluster delete
dcos security org users grant kubernetes-cluster dcos:mesos:master:volume:role:kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:volume:principal:kubernetes-cluster delete

dcos security org users grant kubernetes-cluster dcos:secrets:default:/kubernetes-cluster/* full
dcos security org users grant kubernetes-cluster dcos:secrets:list:default:/kubernetes-cluster read

dcos security org users grant kubernetes-cluster dcos:adminrouter:ops:ca:rw full
dcos security org users grant kubernetes-cluster dcos:adminrouter:ops:ca:ro full

dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:slave_public/kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:slave_public/kubernetes-cluster-role read
dcos security org users grant kubernetes-cluster dcos:mesos:master:reservation:role:slave_public/kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:volume:role:slave_public/kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:slave_public read
dcos security org users grant kubernetes-cluster dcos:mesos:agent:framework:role:slave_public read
```
