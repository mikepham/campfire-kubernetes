# campfire-kubernetes

## Requirements

- export KUBE_DIGITALOCEAN_APIKEY="<apikey>"

- doctl

```
curl -sL https://github.com/digitalocean/doctl/releases/download/v1.43.0/doctl-1.43.0-linux-amd64.tar.gz | tar -xzv && mv doctl /bin
```

- kubectl

```
curl -sLO https://storage.googleapis.com/kubernetes-release/release/`curl \
  -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl && chmod +x kubectl && mv kubectl /bin
  ;
```

### Create Cluster and Configure Local Access
```
doctl kubernetes cluster create campfire
doctl kubernetes cluster kubeconfig save campfire
```

### Install Helm Dependencies

```
# Install Nginx Ingress
helm repo add nginx-stable https://helm.nginx.com/stable
helm install nginx-ingress nginx-stable/nginx-ingress \
  --set controller.publishService.enabled=true,controller.replicaCount=3 \
  ;

# Install DNS
helm install external-dns bitnami/external-dns \
  -f external-dns.yaml \
  ;

# Install Cert Manager
helm repo add jetstack https://charts.jetstack.io
kubectl apply --validate=false \
  -f https://github.com/jetstack/cert-manager/releases/download/v0.15.2/cert-manager.crds.yaml \
  ;
helm install cert-manager jetstack/cert-manager --version v0.15.2
```

### Delete the Cluster

```
doctl kubernetes cluster delete campfire
```
