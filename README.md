# Create VM

On Linux

```
uvt-kvm create --memory 16384 eck release=jammy --bridge br0 --cores 4 --disk 40 --password ubuntu
```

# Install K33

```
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
```

Add `export KUBECONFIG=/etc/rancher/k3s/k3s.yaml` to your .bashrc

Download [k9s](https://k9scli.io/) from https://github.com/derailed/k9s/releases

## Install Longhorn

Following [K3S: Storage](https://docs.k3s.io/storage) set up a local storage provider

```
kubectl apply 0-longhorn.yaml 
kubectl create -f 1-pvc-30GB.yaml 
kubectl create -f 2-pod.yaml 

# Install ECK

## Install CRD and Operator



