# Tsuru Helm Chart

## Prerequisites Details
* Kubernetes 1.6+

## Chart Details
This chart will do the following:

* Implemented a scalable Tsuru cluster using Kubernetes

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm dep up tsuru-helm-chart
$ helm install --name my-release tsuru-helm-chart 
```

## Minikube 

Use branch minikube to use Tsuru on minikube. 

```bash
$ tsuru target-add default http://tsuru.local:32080/
$ tsuru cluster-add default kubernetes --addr https://kubernetes --default --cacert ~/.minikube/ca.crt --clientkey /root/.minikube/client.key --clientcert  /root/.minikube/client.crt
$ tsuru node-list
```
