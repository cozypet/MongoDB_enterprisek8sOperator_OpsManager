# MongoDB_enterprisek8sOperator_OpsManager

-----

## Setup your EC2

Choose your machine
![image](https://github.com/cozypet/MongoDB_enterprisek8sOperator_OpsManager/assets/7107896/d445b1d3-e091-4665-87c3-ae802dad229d)

Configure volume size to 30 GB
![image](https://github.com/cozypet/MongoDB_enterprisek8sOperator_OpsManager/assets/7107896/596991dd-72b4-4840-9e77-5cbc66d8a84d)

![image](https://github.com/cozypet/MongoDB_enterprisek8sOperator_OpsManager/assets/7107896/66faf69a-e851-430b-98e8-576e51566fbe)


## Update the system packages on Ubuntu 22.04 LTS AWS EC2

``` sudo apt update ```
#1.Install kubectl on Ubuntu 22.04 LTS
Download kubectl binary with curl on Ubuntu using below command

curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
Make the kubectl binary executable

chmod +x ./kubectl
Move kubectl to /usr/local/bin/kubectl directory

sudo mv ./kubectl /usr/local/bin/kubectl
To check kubectl version on Ubuntu

kubectl version
Output:

Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.4", GitCommit:"b695d79d4f967c403a96986f1750a35eb75e75f1", GitTreeState:"clean", BuildDate:"2021-11-17T15:48:33Z", GoVersion:"go1.16.10", Compiler:"gc", Platform:"linux/amd64"}
#2.Install Docker on Ubuntu 22.04 LTS
Install below packages before installing docker, you can use official docker site

sudo apt-get install  ca-certificates curl gnupg lsb-release
Add Docker official GPG Key

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
Setup Docker repository using below command

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
Update the package to take effect

sudo apt-get update
Install Docker on Ubuntu 22.04 LTS using below command

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
To check docker service status on Ubuntu

sudo systemctl status docker
Create group named docker

sudo groupadd docker
Configure to Run docker without sudo permission

sudo usermod -aG docker $USER && newgrp docker
To enable docker service at system startup

sudo systemctl enable docker
To check status of docker service

sudo systemctl status docker
To start/stop docker service

sudo systemctl start/stop docker
#3.Install cri-dockerd on Ubuntu 22.04 LTS
The default network plugin for cri-dockerd is set to cni on Linux/Ubuntu for new Kubernetes version as you can follow GitHub Official repo for same

Clone the below git repo

git clone https://github.com/Mirantis/cri-dockerd.git
To install, on a Linux system that uses systemd, and already has Docker Engine installed

wget https://storage.googleapis.com/golang/getgo/installer_linux
chmod +x ./installer_linux
./installer_linux
source ~/.bash_profile
cd cri-dockerd
mkdir bin
go build -o bin/cri-dockerd
Note: Please wait above command takes some time to complete.

mkdir -p /usr/local/bin
sudo install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
sudo cp -a packaging/systemd/* /etc/systemd/system
sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
sudo systemctl daemon-reload
sudo systemctl enable cri-docker.service
sudo systemctl enable --now cri-docker.socket
#4.Install conntrack package on Ubuntu 22.04 LTS
Install conntrack using apt for minikube on Ubuntu 22.04 LTS

sudo apt-get install -y conntrack
#5.Install crictl package on Ubuntu 22.04 LTS
Install crictl: CLI for kubelet CRI on Ubuntu using below commands follow official crictl GitHub page

VERSION="v1.24.2"
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz
sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin
rm -f crictl-$VERSION-linux-amd64.tar.gz
#6.Download and Install Minikube on Ubuntu 22.04 LTS
Download and Install Minikube on Ubuntu 22.04 LTS using below commands, To download latest minikube setup refer minikube official download page

curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
Make the minikube binary executable

chmod +x minikube
Move minikube to /usr/local/bin/kubectl directory

sudo mv minikube /usr/local/bin/
OR

You can use below command to download latest minikube

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
Install Minikube on Ubuntu 22.04 LTS AWS EC2

sudo install minikube-linux-amd64 /usr/local/bin/minikube
To check minikube version on ubuntu

minikube version
Output:

minikube version: v1.24.0
commit: 76b94fb3c4e8ac5062daf70d60cf03ddcc0a741b
Start the minikube Kubernetes cluster on Ubuntu with calico network plugin

minikube start --network-plugin=cni --cni=calico
