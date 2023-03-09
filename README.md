# almalinux-kubespray

Pre: Install VirtualBox & Vagrant 

Vagrant + AlmaLinux 9 + Kubespray testing:

```
$ vagrant up
$ vagrant ssh node-1
$ ssh-keygen 
$ ssh-copy-id 192.168.56.11
$ ssh-copy-id 192.168.56.12
$ ssh-copy-id 192.168.56.13
$ git clone https://github.com/kubernetes-sigs/kubespray
$ cd kubespray/
$ sudo dnf install epel-release
$ sudo dnf install ansible
$ sudo dnf install python3-pip
$ pip3 install -r requirements-2.12.txt 
$ cp -rfp inventory/sample inventory/mycluster
$ declare -a IPS=(192.168.56.11 192.168.56.12 192.168.56.13)
$ CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
$ ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root cluster.yml -e 'ansible_python_interpreter=/usr/bin/python3'
...
PLAY RECAP *******************************************************************************************************************************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
node1                      : ok=753  changed=145  unreachable=0    failed=0    skipped=1258 rescued=0    ignored=8   
node2                      : ok=659  changed=134  unreachable=0    failed=0    skipped=1095 rescued=0    ignored=3   
node3                      : ok=576  changed=113  unreachable=0    failed=0    skipped=791  rescued=0    ignored=2   

=============================================================================== 
kubernetes/preinstall : Install packages requirements --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 103.87s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 91.30s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 84.23s
network_plugin/calico : Wait for calico kubeconfig to be created ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 61.03s
download : download_file | Download item ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 44.38s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 37.72s
kubernetes/kubeadm : Join to cluster --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 33.21s
kubernetes/control-plane : kubeadm | Initialize first master --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 30.81s
kubernetes/control-plane : Joining control plane node to the cluster. ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ 30.46s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 25.41s
etcd : Gen_certs | Write etcd member/admin and kube_control_plane client certs to other etcd nodes ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 23.72s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 23.65s
etcd : reload etcd --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 23.40s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 22.48s
download : download_file | Download item ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 21.40s
container-engine/containerd : download_file | Download item ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 18.17s
kubernetes-apps/ansible : Kubernetes Apps | Start Resources ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 16.83s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 16.25s
download : download_container | Download image if required ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 14.75s
download : download_file | Download item ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- 14.60s

# kubectl get node -o wide
NAME    STATUS   ROLES           AGE     VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION                CONTAINER-RUNTIME
node1   Ready    control-plane   8m44s   v1.26.2   192.168.56.11   <none>        AlmaLinux 9.1 (Lime Lynx)   5.14.0-162.6.1.el9_1.x86_64   containerd://1.6.19
node2   Ready    control-plane   8m9s    v1.26.2   192.168.56.12   <none>        AlmaLinux 9.1 (Lime Lynx)   5.14.0-162.6.1.el9_1.x86_64   containerd://1.6.19
node3   Ready    <none>          6m38s   v1.26.2   192.168.56.13   <none>        AlmaLinux 9.1 (Lime Lynx)   5.14.0-162.6.1.el9_1.x86_64   containerd://1.6.19

[root@node1 ~]# kubectl get all --all-namespaces 
NAMESPACE     NAME                                           READY   STATUS    RESTARTS        AGE
kube-system   pod/calico-kube-controllers-7967fb4566-8zxdx   1/1     Running   0               3m58s
kube-system   pod/calico-node-f6wzm                          1/1     Running   0               6m14s
kube-system   pod/calico-node-q7t6f                          1/1     Running   1 (4m36s ago)   6m14s
kube-system   pod/calico-node-tjxzq                          1/1     Running   0               6m14s
kube-system   pod/coredns-68868dc95b-chpdj                   1/1     Running   0               3m34s
kube-system   pod/coredns-68868dc95b-hd7zp                   1/1     Running   0               3m20s
kube-system   pod/dns-autoscaler-7ccd65764f-5trm7            1/1     Running   0               3m25s
kube-system   pod/kube-apiserver-node1                       1/1     Running   1               9m17s
kube-system   pod/kube-apiserver-node2                       1/1     Running   1               8m40s
kube-system   pod/kube-controller-manager-node1              1/1     Running   3 (101s ago)    9m17s
kube-system   pod/kube-controller-manager-node2              1/1     Running   2 (4m28s ago)   8m40s
kube-system   pod/kube-proxy-ckrh5                           1/1     Running   0               7m12s
kube-system   pod/kube-proxy-k4n4f                           1/1     Running   0               7m12s
kube-system   pod/kube-proxy-np9np                           1/1     Running   0               7m12s
kube-system   pod/kube-scheduler-node1                       1/1     Running   3 (4m27s ago)   9m17s
kube-system   pod/kube-scheduler-node2                       1/1     Running   4 (102s ago)    8m40s
kube-system   pod/nginx-proxy-node3                          1/1     Running   0               6m4s
kube-system   pod/nodelocaldns-dzg6p                         1/1     Running   0               3m22s
kube-system   pod/nodelocaldns-gpbx8                         1/1     Running   0               3m22s
kube-system   pod/nodelocaldns-skdsc                         1/1     Running   0               3m22s

NAMESPACE     NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   10.233.0.1   <none>        443/TCP                  9m19s
kube-system   service/coredns      ClusterIP   10.233.0.3   <none>        53/UDP,53/TCP,9153/TCP   3m33s

NAMESPACE     NAME                          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/calico-node    3         3         3       3            3           kubernetes.io/os=linux   6m15s
kube-system   daemonset.apps/kube-proxy     3         3         3       3            3           kubernetes.io/os=linux   9m17s
kube-system   daemonset.apps/nodelocaldns   3         3         3       3            3           kubernetes.io/os=linux   3m23s

NAMESPACE     NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/calico-kube-controllers   1/1     1            1           4m10s
kube-system   deployment.apps/coredns                   2/2     2            2           3m35s
kube-system   deployment.apps/dns-autoscaler            1/1     1            1           3m31s

NAMESPACE     NAME                                                 DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/calico-kube-controllers-7967fb4566   1         1         1       3m59s
kube-system   replicaset.apps/coredns-68868dc95b                   2         2         2       3m35s
kube-system   replicaset.apps/dns-autoscaler-7ccd65764f            1         1         1       3m30s




```
