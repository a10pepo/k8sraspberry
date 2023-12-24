# k8sraspberry

The goal of this repo is to build a kubernetes cluster on raspberry pi 4.

## Hardware

- 1 raspberry pi 4 (4GB)

Setting up the hardware is pretty straightforward. Just plug in the power cable, the ethernet cable and the sd card with the raspbian image.

## Software

- Raspberry Pi OS Lite (64-bit) Distribution

### Pre-step

```bash
sudo nano /boot/cmdline.txt
sudo apt update
sudo apt upgrade
sudo apt install bash-completion
```

Add this to the end of the line:

**cgroup_memory=1 cgroup_enable=memory**

### Install k3s

```bash
curl -sfL https://get.k3s.io | sh -
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

### Install kubectl, kubectx and helm

```bash
sudo apt install snapd
sudo snap install kubectl --classic
sudo snap install kubectx --classic
sudo snap install helm --classic
sudo snap install kubelet --classic

echo "alias k=/snap/kubectl/current/kubectl" >> ./.bashrc
echo "alias helm=/snap/helm/current/helm" >> ./.bashrc
echo "alias kubens=/snap/kubectx/current/kubens" >> ./.bashrc

source ./.bashrc
```

### install argocd using helm

```bash
k create namespace argocd
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd -n argocd -f values-argocd.yaml

```


### Restart k3s

```bash
sudo /usr/local/bin/k3s-uninstall.sh 
curl -sfL https://get.k3s.io | sh -
sudo chmod 777 /etc/rancher/k3s/k3s.yaml
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

### Install Kubernetes Dashboard

```bash
k create namespace kubernetes-dashboard
kubens kubernetes-dashboard
helm repo add k8s-dashboard https://kubernetes.github.io/dashboard
helm template k8s-dashboard/kubernetes-dashboard --version 7.0.0-alpha1 -f values-dashboard.yaml > dashboard-gen.yaml  
k apply -f dashboard-gen.yaml
```
NOTE: Change default traffic management to traefik instead of internal-ngnix



### Install Istio 1.17.1

Useful commands:


```bash
# clean first if needed
helm list -A
helm uninstall istio-base -n istio-system
helm uninstall istiod -n istio-system
helm uninstall istio-gateway -n istio-system
helm show values istio/istiod --version 1.17.1
# In case of error installing
#Error: INSTALLATION FAILED: Internal error occurred: failed calling webhook "validation.istio.io": failed to call webhook: Post "https://istiod.istio-system.#svc:443/validate?timeout=10s": no endpoints available for service "istiod"
kubectl delete validatingwebhookconfiguration istiod-default-validator 
```

```bash

export ISTIO_VERSION=1.17.1
export CERT_MANAGER_VERSION=v1.13.3

helm search repo istio/base

helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo add jetstack https://charts.jetstack.io

helm repo update
k create namespace istio-system
helm install istio-base istio/base -n istio-system --create-namespace --version $ISTIO_VERSION
helm install istiod istio/istiod --namespace istio-system --version $ISTIO_VERSION --create-namespace --set meshConfig.ingressService=istio-gateway --set meshConfig.ingressSelector=gateway --set meshConfig.defaultConfig.holdApplicationUntilProxyStarts=true
helm install istio-gateway istio/gateway --namespace istio-gateway --version $ISTIO_VERSION --create-namespace
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/$CERT_MANAGER_VERSION/cert-manager.crds.yaml
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version $CERT_MANAGER_VERSION --create-namespace 

```






