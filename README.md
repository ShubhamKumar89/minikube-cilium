# cilium

## Pre-requisite

1. `kind` cluster

```bash
curl -LO https://raw.githubusercontent.com/cilium/cilium/v1.13/Documentation/installation/kind-config.yaml
kind create cluster --config=kind-config.yaml
```

2. `kubectl` CLI

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo apt-get install -y apt-transport-https
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```


