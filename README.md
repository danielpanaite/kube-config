# Cluster Config Notes

## Info

Cluster join command
```bash
kubeadm join 10.0.0.10:6443 --token usgve4.t9wyhyoofo7q589g \
        --discovery-token-ca-cert-hash sha256:3179dffe90ca670ebf9ceee30d6337e8af04d0a722d988a7477d62a98b375778
```

## Prepare cluster node

Disable swap on all the nodes:

```bash
sudo swapoff -a
sudo nano /etc/fstab
```

Update the apt package index and install packages needed to use the Kubernetes apt repository:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl docker.io

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

Add the Kubernetes apt repository and update:

```bash
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list 

sudo apt-get update 
sudo apt-get install -y kubelet=1.23.6-00 kubeadm=1.23.6-00 kubectl=1.23.6-00 
sudo apt-mark hold kubelet kubeadm kubectl
```

## Init

Initialize

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

Copy base config

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Untaint master node

```bash
kubectl taint nodes --all node-role.kubernetes.io/master-
```

Flannel CNI init for k8s 1.23.6

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/a1a24eb453825189b7092006edfdf65e933cbc3c/Documentation/kube-flannel.yml
```

## Metallb Load Balancer

```bash
kubectl apply -f ./metallb/metallb.yaml
kubectl apply -f ./metallb/ipaddresses.yaml
```

## NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.5.1/deploy/static/provider/cloud/deploy.yaml
```

v1.5.1 still works with k8s 1.23 but please check!

## Technitium DNS server

```bash
kubectl apply -f ./local-storage-class.yaml

kubectl apply -f technitium/namespace.yaml -f technitium/configmaps.yaml -f technitium/secrets.yaml -f technitium/pv.yaml -f technitium/pvcs.yaml -f technitium/deployments.yaml -f technitium/services.yaml
```