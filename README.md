# k8sraspberry

The goal of this repo is to build a kubernetes cluster on raspberry pi 4.

## Hardware

- 1 raspberry pi 4 (4GB)

Setting up the hardware is pretty straightforward. Just plug in the power cable, the ethernet cable and the sd card with the raspbian image.

## Software

### Install Docker

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates software-properties-common
curl -fsSL https://download.docker.com/linux/raspbian/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=armhf] https://download.docker.com/linux/raspbian $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER
docker --version
``` 

### Install kubeadm, kubelet, k9s and kubectl

```bash

```

### Install minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm64
sudo install minikube-linux-arm64 /usr/local/bin/minikube
minikube version
```

### Starting minikube
    
```bash
minikube start --driver=docker

```

### Install ArgoCD

```bash





