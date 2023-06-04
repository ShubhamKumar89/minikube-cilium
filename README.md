# cilium

Installing Cilium CNI in kubernetes cluster. [Official Site](https://docs.cilium.io/en/v1.13/gettingstarted/k8s-install-default/#cilium-quick-installation).

## Pre-requisite

#### 1. [Install docker](https://docs.docker.com/engine/install/ubuntu/)

```bash
# update packages
sudo apt-get update
sudo apt-get upgrade

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
 
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# run with sudo
sudo usermod -aG docker ${USER}
su - ${USER}
sudo chmod 666 /var/run/docker.sock
```

#### 2. [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management)

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo apt-get install -y apt-transport-https
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

## Procedure 

#### 1. Create cluster

In my case, I am using `minikube` cluster. [Install minikube](https://minikube.sigs.k8s.io/docs/start/)

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Now its time to bring up a single node minikube cluster prepared for installing cilium.

```bash
minikube start --network-plugin=cni --cni=false --driver=docker
```

> **Note**: <br>
> From minikube v1.12.1+, cilium networking plugin can be enabled directly with `--cni=cilium` parameter in `minikube start` command. However, this may not install the latest version of cilium.

#### 2. Install Cilium CLI

Install the latest version of the Cilium CLI. The Cilium CLI can be used to install Cilium, inspect the state of a Cilium installation, and enable/disable various features (e.g. clustermesh, Hubble).

```bash
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/master/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```

#### 3. Install Cilium

Linux kernel >= `4.9.17`

```bash
cilium install
```

#### 4. Validate the Installation

```bash
cilium status --wait
```

![image](https://github.com/ShubhamKumar89/cilium/assets/97805339/bf3519b2-0bb0-486d-a4b0-aa673ece92fc)

Run the following command to validate that your cluster has proper network connectivity:

```bash
cilium connectivity test
```

![image](https://github.com/ShubhamKumar89/cilium/assets/97805339/d2c235b8-f1bd-4ff0-9b7f-018d3d5c9af7)
