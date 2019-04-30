# Prequisite
    Install crio 
        edit crio.conf with cgroup_manager="cgroupfs"
    install podman 

# Kubeadmin init: In a multi VM environment its better to use
  flag "apiserver-advertise-address" with the master vm ip


# Initialize
  [vagrant@master ~]$ sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=10.0.0.10

# Calico
  [vagrant@master ~]$ kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml

  [vagrant@master ~]$ kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml

# Sample cheatsheet commands

    [vagrant@master ~]$ kubectl get pods --all-namespaces
    NAMESPACE     NAME                             READY   STATUS              RESTARTS   AGE
    kube-system   calico-node-9qnl7                2/2     Running             0          12s
    kube-system   coredns-fb8b8dccf-9vjqg          0/1     ContainerCreating   0          2m39s
    kube-system   coredns-fb8b8dccf-h2k2q          0/1     ContainerCreating   0          2m39s
    kube-system   etcd-master                      1/1     Running             0          118s
    kube-system   kube-apiserver-master            1/1     Running             0          2m    
    kube-system   kube-controller-manager-master   1/1     Running             0          110s
    kube-system   kube-proxy-6mbz9                 1/1     Running             0          2m40s
    kube-system   kube-scheduler-master            1/1     Running             0          98s

    [vagrant@master ~]$ kubectl version -o json
    {
      "clientVersion": {
    "major": "1",
    "minor": "14",
    "gitVersion": "v1.14.1",
    "gitCommit": "b7394102d6ef778017f2ca4046abbaa23b88c290",
    "gitTreeState": "clean",
    "buildDate": "2019-04-08T17:11:31Z",
    "goVersion": "go1.12.1",
    "compiler": "gc",
    "platform": "linux/amd64"
      }
    }

# The connection to the server localhost:8080 was refused - did you specify the right host or port?

    [vagrant@master ~]$ sudo crictl ps 
     CONTAINER ID        IMAGE                                                              CREATED             STATE               NAME                      ATTEMPT             POD ID
    da6f8d78f6d18       eb516548c180f8a6e0235034ccee2428027896af16a509786da13022fe95fe8c   39 minutes ago      Running             coredns                   1                   e8eed2acb7376
    a8c040d5d3125       eb516548c180f8a6e0235034ccee2428027896af16a509786da13022fe95fe8c   39 minutes ago      Running             coredns                   1                   7db53957eff28
    8ec02d6ae1320       20a2d7035165926828d874302769d26feb6ba80db3965a006bfaa13cf2508286   40 minutes ago      Running             kube-proxy                0                   3bf7189ee331e
    02ddd285a74fd       2c4adeb21b4ff8ed3309d0e42b6b4ae39872399f7b37e0856e673b13c4aba13d   40 minutes ago      Running             etcd                      0                   a4e1c2f662270
    7c35fa7e90756       cfaa4ad74c379e428b953c9aa9962e25a6de470a38b3b62ea2feaef1bfb30e0d   40 minutes ago      Running             kube-apiserver            0                   324f58ee5e648
    bd88fdefa7908       8931473d5bdb74cb743cb3c0bac924f1e19cd728cc8eecf7305af583c0755bf8   40 minutes ago      Running             kube-scheduler            0                   c6cdf24647a1b
    074f266e02f92       efb3887b411d215e2aec3682b2a8ecfbd2926c3e01f730954be67f53132c97fb   40 minutes ago      Running             kube-controller-manager   0                   a4b8359a1c2ff


    # resolution:
        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Cluster Info
    vagrant@master ~]$ kubectl version -o json
    {
    "clientVersion": {
        "major": "1",
        "minor": "14",
        "gitVersion": "v1.14.1",
         "gitCommit": "b7394102d6ef778017f2ca4046abbaa23b88c290",
         "gitTreeState": "clean",
         "buildDate": "2019-04-08T17:11:31Z",
          "goVersion": "go1.12.1",
          "compiler": "gc",
          "platform": "linux/amd64"
     },
      "serverVersion": {
         "major": "1",
          "minor": "14",
           "gitVersion": "v1.14.1",
           "gitCommit": "b7394102d6ef778017f2ca4046abbaa23b88c290",
          "gitTreeState": "clean",
          "buildDate": "2019-04-08T17:02:58Z",
           "goVersion": "go1.12.1",
           "compiler": "gc",
          "platform": "linux/amd64"
        }
        }

        [vagrant@master ~]$ kubectl cluster-info
          Kubernetes master is running at https://10.0.2.15:6443
          KubeDNS is running at https://10.0.2.15:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy


#   To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.


[vagrant@master ~]$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://10.0.2.15:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED

# Crictl Ps
    [vagrant@node1 ~]$ sudo crictl ps
    CONTAINER ID        IMAGE                                                                                           CREATED             STATE               NAME                ATTEMPT             POD ID
    7bfeeed7b436d       docker.io/calico/cni@sha256:705666b563ab928518225c315d73002255c9f86827bc069eb02e35f55064c897    5 minutes ago       Running             install-cni         0                   196acf5675f83
    8dbcf936565e1       k8s.gcr.io/kube-proxy@sha256:020d25ff45a33ec7958d7128308cb499d5b24cdaa228a2344514bcab9b7296c0   5 minutes ago       Running             kube-proxy          0                   3bbfa9091123f
    dbf1a8631d5a0       docker.io/calico/node@sha256:f288d3586e552d748dc655db5c9a75a8c6603963347a64e8b7dc264ea6bb91b0   5 minutes ago       Running             calico-node         0                   196acf5675f83


#   Deleteing the node
    kubectl drain <node-name>
    kubectl drain <node-name> --ignore-daemonsets --delete-local-data
    kubectl delete node <node-name>


    # https://kubernetes.io/docs/reference/kubectl/cheatsheet/



#   Ok, so now that we're tired of typing commands out, let's enable bash auto-complete of our kubectl commands
     sudo apt-get install bash-completion
     echo "source <(kubectl completion bash)" >> ~/.bashrc
     source ~/.bashrc
     kubectl g[tab][tab] po[tab][tab] --all[tab][tab]



     kubectl -v=9 exec -it etcd-master -- /bin/sh
    
     kubectl get nodes master -o json | jq .status.addresses


#   Resolving the internal-ip of the node
     sudo vi /var/lib/kubelet/kubeadm-flags.env

    Add "--node-ip" to '/var/lib/kubelet/kubeadm-flags.env':
    [root@Node-18121 ~]# cat /var/lib/kubelet/kubeadm-flags.env
    KUBELET_KUBEADM_ARGS=--cgroup-driver=systemd --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.1 --node-     ip=10.10.10.1
    Restart Kubelet:
    systemctl daemon-reload && systemctl restart kubelet

    [vagrant@master ~]$ kubectl get nodes -o wide
    NAME     STATUS   ROLES    AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION               CONTAINER-RUNTIME
    master   Ready    master   22h     v1.14.1   10.0.2.15     <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64     cri-o://1.11.11-1.rhaos3.11.git474f73d.el7
    node1    Ready    <none>   15m     v1.14.1   10.0.0.11     <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64     cri-o://1.11.11-1.rhaos3.11.git474f73d.el7
    node2    Ready    <none>   9m18s   v1.14.1   10.0.2.15     <none>        CentOS Linux 7 (Core)   3.10.0-957.5.1.el7.x86_64     cri-o://1.11.11-1.rhaos3.11.git474f73d.el7

    I changed the node ip of node1 and its now updated to 10.0.0.11 the ip of ETH1 of vagrant machine.


# Error: Failed to get system container stats for "/system.slice/kubelet.service":
    Refer https://github.com/kubernetes/kops/issues/4049
    Resolution :
      sudo vim /etc/sysconfig/kubelet

        add at the end of DAEMON_ARGS string:

        --runtime-cgroups=/systemd/system.slice --kubelet-cgroups=/systemd/system.slice

      finally:

        sudo systemctl restart kubelet
        
 # Error like:
        Failed to list *v1beta1.CSIDriver: Get https://10.0.0.10:6443
        Apr 23 09:39:08 master kubelet[7419]: E0423 09:39:08.127284    7419 reflector.go:126] k8s.io/client-                           go/informers/factory.go:133: Failed to list *v1beta1.RuntimeClass: Get https://10.0.0.10:6
        k8s.io/kubernetes/pkg/kubelet/kubelet.go:442: Failed to list *v1.Service: Get https://10.0.0.10:6443/api
        k8s.io/kubernetes/pkg/kubelet/config/apiserver.go:47: Failed to list *v1.Pod: Get https://10.0.0.10:6443
    
       Resolution:
       #cgroup_manager is the cgroup management implementation to be used
        for the runtime.
        cgroup_manager = "cgroupfs
