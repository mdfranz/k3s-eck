# Create VM

On Linux

```
uvt-kvm create --memory 16384 --disk 40 --cpu 4 --password ubuntu k3sg3a release=noble
```

or

```
uvt-kvm create --memory 8192 eck1-hpg3 release=jammy --disk 40 --bridge br0 --cpu 4 --password ubuntu

```

or 

```
multipass launch -m 16G -c8 -d 40G --name k3s-16gb noble
```


# Install K33

```
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" INSTALL_K3S_EXEC="--disable=traefik" sh -s -
```

Add `export KUBECONFIG=/etc/rancher/k3s/k3s.yaml` to your .bashrc

(If you don't you will get a plugin failed error)

Download [k9s](https://k9scli.io/) from https://github.com/derailed/k9s/releases

## Install Nginx Ingress Controller

```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm update
kubectl create namespace nginx
helm install ingress-nginx ingress-nginx/ingress-nginx --namespace nginx
```

## Install Longhorn

Following [K3S: Storage](https://docs.k3s.io/storage) set up a local storage provider

```
kubectl apply 0-longhorn.yaml 
```
If you have 2 node cluster you'll see something like this

```
ubuntu@optiplex-master:~$ kubectl get services -n longhorn-system -o wide
NAME                          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE     SELECTOR
longhorn-backend              ClusterIP   10.43.181.204   <none>        9500/TCP   8m48s   app=longhorn-manager
longhorn-frontend             ClusterIP   10.43.88.173    <none>        80/TCP     8m48s   app=longhorn-ui
longhorn-conversion-webhook   ClusterIP   10.43.17.46     <none>        9501/TCP   8m48s   app=longhorn-manager
longhorn-admission-webhook    ClusterIP   10.43.198.40    <none>        9502/TCP   8m48s   app=longhorn-manager
longhorn-recovery-backend     ClusterIP   10.43.135.215   <none>        9503/TCP   8m48s   app=longhorn-manager
longhorn-engine-manager       ClusterIP   None            <none>        <none>     8m48s   longhorn.io/component=instance-manager,longhorn.io/instance-manager-type=engine
longhorn-replica-manager      ClusterIP   None            <none>        <none>     8m48s   longhorn.io/component=instance-manager,longhorn.io/instance-manager-type=replica
```

And you'll connect to *longhorn-ui* (I use SSH port forwarding through Firefox so I can access cluster IPs)

Or 

## Annotate the UI to expose via Tailscale

Install the [tailscale k8s operator](https://tailscale.com/learn/managing-access-to-kubernetes-with-tailscale#using-tailscales-kubernetes-operator)

add `tailscale.com/expose: "true"` to the Service annotation

```
~$ kubectl get service longhorn-frontend -n longhorn-system -o yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"labels":{"app":"longhorn-ui","app.kubernetes.io/instance":"longhorn","app.kubernetes.io/name":"longhorn","app.kubernetes.io/version":"v1.8.0-dev"},"name":"longhorn-frontend","namespace":"longhorn-system"},"spec":{"ports":[{"name":"http","nodePort":null,"port":80,"targetPort":"http"}],"selector":{"app":"longhorn-ui"},"type":"ClusterIP"}}
    tailscale.com/expose: "true"
  creationTimestamp: "2024-08-17T23:01:08Z"
  finalizers:
  - tailscale.com/finalizer
  labels:
    app: longhorn-ui
    app.kubernetes.io/instance: longhorn
    app.kubernetes.io/name: longhorn
    app.kubernetes.io/version: v1.8.0-dev
  name: longhorn-frontend
  namespace: longhorn-system
```


# Install ECK

## Install CRD and Operator

Follow the instructions from the first part of the [quickstart](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-deploy-eck.html)

## Get secrets

Use `elastic` with the following password to login to Kibana

```
kubectl get secret elasticsearch-quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode; echo
```

