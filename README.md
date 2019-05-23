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

Install the ingress addon on minikube.

```bash
$ minikube addons enable ingress
$ helm install --name my-release tsuru-helm-chart --set nginx-ingress.controller.service.type=NodePort \
                                                  --set ingress.domain=local.$(minikube ip).nip.io \
                                                  --set ingress.tls[0].hosts[0]=tsuru.local.$(minikube ip).nip.io \
                                                  --set docker-registry.ingress.hosts[0]=registry.local.$(minikube ip).nip.io \
                                                  --set docker-registry.ingress.tls[0].hosts[0]=registry.local.$(minikube ip).nip.io \
                                                  --set ingress.tls[0].secretName=tsuru-api \
                                                  --set docker-registry.ingress.tls[0].secretName=registry-tls
$ tsuru target-add default http://tsuru.local.$(minikube ip).nip.io/
$ tsuru cluster-add default kubernetes --addr https://kubernetes --default --cacert ~/.minikube/ca.crt --clientkey /root/.minikube/client.key --clientcert  /root/.minikube/client.crt
$ tsuru node-list
```

## ACME/Kube-lego (Automatic Certificate Management Environment)

By default values will keep using the testing api endpoint for Let's encrypt, that means the certificates will not be valid.

```bash
helm install --name my-release --set ingress.domain=prod.us.cloud.domain.com,kube-lego.config.LEGO_EMAIL=admin@domain.com,kube-lego.config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory tsuru-helm-chart
```

Then while creating the apps add the --router-opts tls-acme=true. This will enable ACME with Let's Encrypt.

```bash
tsuru app-create tsuru-dashboard python --router-opts tls-acme=true
```

You can still create the chart with ACME support but use own certificates on the application.

## Disable RBAC

By default Tsuru.io will be deployed with serviceaccounts with the necessary access. That can be disabled by the value rbac.create=false.

```bash
helm install --name my-release --set rbac.create=false tsuru-helm-chart
```

## Updating Chart to use latest CertManager

```
helm upgrade tsuru ./  --reuse-values    --set cert-manager.webhook.enabled=false --set cert-manager.cainjector.enabled=false --set ingressShim.defaultIssuerKind=ClusterIssuer --set ingressShim.defaultIssuerName=letsencrypt-prod --set cert-manager.image.tag=v0.8.0
```
