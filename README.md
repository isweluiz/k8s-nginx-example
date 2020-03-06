# k8s-nginx-example

The YaML files provided in the [manifests](manifests) folder can be used to
deploy a simple `NginX` stack to a given Kubernetes cluster, with a pod
providing `curl`.

They are intended to be used for demonstration purposes on `minikube`.

## Requirements

The following needs to be installed on your local workstation:

* [GIT](https://git-scm.com/downloads)
* [minikube](https://kubernetes.io/de/docs/tasks/tools/install-minikube/)

## Installation

Clone this repository:

```bash
git clone https://github.com/tarak/k8s-nginx-example.git && \
cd k8s-nginx-example
```

Make sure you have configured `minikube` as current context:

```bash
kubectl config use-context minikube
```

To use these manifests, run:

```bash
kubectl apply -R -f manifests
```

## Usage

To view only the `nginx` pods, run:

```bash
kubectl get pods -l app=nginx
```

To view only the `curl` pod, run:

```bash
kubectl get pods -l app=curl
```

To identify a `nginx` pod for shell usage, run:

```bash
NGINX_POD=$(kubectl get pod -l app=nginx -o jsonpath="{.items[0].metadata.name}")
```

To identify the `curl` pod for shell usage, run:

```bash
CURL_POD=$(kubectl get pod -l app=curl -o jsonpath="{.items[0].metadata.name}")
```

To run a shell on the `nginx` pod, run:

```bash
kubectl exec ${NGINX_POD} --tty -i -- bash
```

To get a shell on the `curl` pod, run:

```bash
kubectl exec ${CURL_POD} --tty -i -- sh
```

You can also run commands in the `curl` pod directly, as follows:

```bash
kubectl exec ${CURL_POD} -- nslookup nginx
kubectl exec ${CURL_POD} -- nslookup nginx.default.svc.cluster.local

kubectl exec ${CURL_POD} -- curl -v -s http://nginx/
kubectl exec ${CURL_POD} -- curl -v -s http://nginx.default.svc.cluster.local/
```

## Scaling

To scale the deployment up or down, run (i.e. to scale to 3 pods):

```bash
kubectl scale deployment nginx --replicas=3
```

To disable keepalives (and show how the service distributes the requests across
the different pods running) while executing `curl`, add the HTTP request header
`"Connection: close"`:

```bash
kubectl exec ${CURL_POD} -- curl -v -s -H "Connection: close" http://nginx/
```

Search for the `X-Kubernetes-Pod` header in the response of the HTTP request.
You will see that it changes to the names of the `nginx` pods running:

```bash
kubectl get pods -l app=nginx
```

## Browser access

To view the service in a web browser, get the URL of the `nginx` service from
`minikube`:

```bash
minikube service nginx --url
```

Open the resulting URL in your favorite browser. Also use the browser's
developer tools to see the HTTP requests and responses in the browser.

## Uninstallion

To uninstall all objects, run:

```bash
kubectl delete -R -f manifests
```
