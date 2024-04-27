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


# Install ECK

## Install CRD and Operator



