---
layout: default
parent: Kubernetes Lab
title: Scaling a Kubernetes Cluster
nav_order: 3
---

## Scaling Your Kuberenetes Cluster By Adding More Kubelets

Sometimes you need more Kubernetes infrastructure to run more applications. DC/OS can easily scale the cluster.

There are several ways to scale Kubernetes in DC/OS, including via the CLI, HTTP API, or GUI. In this exercise, we will use the DC/OS GUI to scale our Kubernetes cluster.

From the UI, go to Services > Kubernetes.

![](https://github.com/tbaums/dcos-mandt-labs/raw/master/screenshots/select-k8s-1.png)

Next, choose "Edit" in top right.

![](https://github.com/tbaums/dcos-mandt-labs/raw/master/screenshots/select-k8s-edit.png)

Under "kubernetes" in left hand menu, change the number of "node count" to 2. Then select "Review and Run".

![](https://github.com/tbaums/dcos-mandt-labs/raw/master/screenshots/increase-kubelet-count.png)

You will observe that the scheduler task updates, followed by `etcd`. Lastly, the new Kubelet will start up and join the Kubernetes cluster.

![](https://github.com/tbaums/dcos-mandt-labs/raw/master/screenshots/kubelet-starting.png)

Wait for the new node change to `Running` state then you can confirm the additional Kubelet was added sucessfully by running:

```
kubectl get nodes

NAME                                                      STATUS    ROLES     AGE       VERSION
kube-control-plane-0-instance.kubernetes-cluster1.mesos   Ready     master    110m      v1.13.3
kube-control-plane-1-instance.kubernetes-cluster1.mesos   Ready     master    32m       v1.13.3
kube-control-plane-2-instance.kubernetes-cluster1.mesos   Ready     master    32m       v1.13.3
kube-node-0-kubelet.kubernetes-cluster1.mesos             Ready     <none>    108m      v1.13.3
kube-node-1-kubelet.kubernetes-cluster1.mesos             Ready     <none>    7m48s     v1.13.3
kube-node-public-0-kubelet.kubernetes-cluster1.mesos      Ready     <none>    107m      v1.13.3
```
