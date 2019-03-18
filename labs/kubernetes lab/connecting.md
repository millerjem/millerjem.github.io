---
layout: default
parent: Kubernetes Lab
title: Connecting to a Kubernetes Cluster
nav_order: 1
---

# Connecting kubectl to DC/OS

### Step 1
Find public IP address of Public DC/OS Node

Your instructors have pre-installed the DC/OS Edge Load Balancer on your cluster and configured it to provide ingress to your Kuberenetes cluster's control plane.

To attach `kubectl` to your cluster, you must point the configuration command below to the public IP address of the public DC/OS agent where Edge-LB is running.

Your DC/OS cluster contains 1 public agent nodes. To determine where Edge-LB is running, open your `clusterinfo.txt` file and note the IP addresses of your public agent.

Next, attempt to visit the following URL for your public agents:

```
<public agent public IP address>:6090/haproxy?stats
```

![marathon-lb-stats](https://github.com/tbaums/dcos-k8s-days-labs/raw/master/screenshots/marathon-lb%20stats.png)


### Step 2
Connecting using Kubeconfig

Configure kubectl to connect to the Kubernetes cluster running on  DC/OS using the following commands:
```
dcos kubernetes cluster kubeconfig \
    --insecure-skip-tls-verify \
    --context-name=kubernetes-cluster1 \
    --cluster-name=kubernetes-cluster1 \
    --apiserver-url=https://<public agent where Edge-LB is running IP address>:6443
```

Confirm connection:

```
kubectl get nodes
```

### Step 3
Kubernetes Dashboard (Official UI of Kubernetes)

To access the dashboard run, we will expose the Kubernetes UI via a `NodePort` deployment on the Kubernetes cluster that we will access on our DC/OS public agent.

To begin, download `dashboard-ext.yaml` and save it to your local directory with the following command:

```
curl -O https://raw.githubusercontent.com/tbaums/dcos-mandt-labs/master/clusters/setup/dashboard-ext.yaml
```

```
/* Raw content of dashboard-ext.yaml */
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-dashboard-ext
  namespace: kube-system
spec:
  type: NodePort
  selector:
    k8s-app: kubernetes-dashboard
  ports:
  - protocol: TCP
    nodePort: 30443
    port: 8443
```

Next, let's create this deployment on our Kubernetes cluster:

```
kubectl apply -f dashboard-ext.yaml
```
To authenticate to your Kubernetes dashboard, you will need your Kuberenetes `config` file, which contains your authorization token. Let's put the config file in our present working directory for convenience:

```
# Mac instructions
cp ~/.kube/config ./
```

To access your Kubernetes dashboard, point your web browser to the URL below:

*Make sure to include `https://` in your URL*

```
https://<your DC/OS public agent public IP>:30443
```

Lastly, find select your Kubernetes config file from the dialog box and click "Sign in"

![k8s-sign-in](https://github.com/tbaums/dcos-mandt-labs/raw/master/screenshots/k8s-sign-in.png)

Congratulations! You should now see the Kubernetes dashboard.

![k8s-dashboard](https://github.com/tbaums/dcos-mandt-labs/raw/master/screenshots/k8s-dashboard.png)
