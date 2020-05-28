## commands:
`$ kubeadm init --control-plane-endpoint control-plane.null`

NOTE: don't need pod cider as network is a 10. and not a 192.168. and calico uses the 192.168.
NOTE: setting a DNS entry instead of a IP is more flexable

## Installing docker:

NOTE: don't use what your package provider gives as it's outdated.
sudo apt-get remove docker docker-engine docker.io containerd runc

```
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common```

`$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```


### Prep machine

`swapoff -a`

`vim /etc/fstab` and comment out/delete (depending on how comforatable you are)

reboot

### Kubernetes

kubernetes apt key
`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -`

```
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
```

`apt-get update && apt-get install -y kubelet kubeadm kubectl`

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
``` 

NOTE: protect it with your life. it has the master admin key to your cluster!

## Calico install
```
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
```


Remove master taint so it can run workloads
`kubectl taint nodes --all node-role.kubernetes.io/master-`

## TEST!!!
`kubectl run hello --image=k8s.gcr.io/echoserver:1.4 --port=8080`
