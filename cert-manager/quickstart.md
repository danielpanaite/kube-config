# Cert-Manager

## Installation

Please follow the official documentation [here](https://cert-manager.io/docs/installation/helm/)

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.crds.yaml
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.8.0
```
