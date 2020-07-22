# Kubernetes Demo

Kubernetes Demo in which we start up a selenium hub deployment, and then use the selenium browsers to do a web crawl.

## Prerequisites

* [minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
* [python 3](https://python.org)
* [pip](https://pip.pypa.io/en/stable/installing/)
* [jupyter](https://jupyter.org/install.html)
* (Bonus Round) [Helm](https://helm.sh/docs/intro/install/)

---

## Start Minikube Cluster

### 1. Make sure to delete a possible existing minikube cluster

```bash
minikube delete
```

### 2. Start a minikube cluster. For this demo we need 2 cpus & 4G memory

```bash
minikube start --cpus=2 --memory=4G
```

### 3. After the cluster is initialized check if you can start the kubernetes dashboard

The kubernetes dashboard is a visual kubernetes administration tool that shows the current state of the cluster in a nice overview. Normally this should be installed seperately, but with minikube it's included.

```bash
minikube dashboard
```

---

## Setup Selenium hub inside the kubernetes cluster

### 1. Deploy yaml files in folder

```bash
kubectl apply -f .
```

### 2. Forward traffic from selenium-hub to your local machine

```bash
kubectl port-forward service/selenium-hub 4444:4444
```

---

## Run Crawl Example

Run the cells in the [crawl_example](./crawl_example.ipynb) jupyter notebook to execute a web crawl that will fetch email adresses from some pages. The script will use a selenium browser from the kubernetes cluster.

**Congrats!, you've deployed a scalable & usable selenium grid inside a local kubernetes cluster.**

---

## (Bonus Round) Switch from Selenium to Zalenium

To fully show the ease of deployment in kubernetes we'll switch the selenium deployment with a 'Zalenium' Deployment. This is a clone of selenium with a lot of extra features.

### 1. Delete selenium deployment

```bash
kubectl delete -f .
```

### 2. Clone Zalenium repostiroy & switch to folder

```bash
git clone https://github.com/zalando/zalenium
cd zalenium
```

### 3. Create namespace for zalenium

```bash
kubectl create namespace zalenium
```

### 4. Install zalenium with Helm

```bash
helm install zalenium --namespace zalenium charts/zalenium --set hub.serviceType=NodePort
```

### 5. Check in kubernetes dashboard if all pods are alive

### 6. Forward traffic from zalenium to your local machine

```bash
kubectl port-forward -n zalenium service/zalenium 4444:80
```

Check [this url](http://localhost:4444/grid/admin/live) to see if the zalenium admin page shows up. If so, run the crawl_example notebook again to see the crawl happening on the admin page.
