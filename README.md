# operator-framework usage for kubedge

## Initialization of kubedge-operator-go

```bash
operator-sdk new kubedge-operator-ansible --api-version=arpscan.kubedge.cloud/v1alpha1 --kind=Kubedge --type=ansible
cd kubedge-operator-ansible
git remote add origin git@github.com:kubedge/kubedge-operator-ansible
git push -u origin master
```

## Coding the kubedge-operator-go

```bash
operator-sdk add api --api-version=arpscan.kubedge.cloud/v1alpha1 --kind=Kubedge
vi pkg/apis/arpscan/v1alpha1/kubedge_types.go 
operator-sdk generate k8s
operator-sdk add controller --api-version=arpscan.kubedge.cloud/v1alpha1 --kind=Kubedge
cp /home/kubedge/proj/jbrette/operator-sdk/example/memcached-operator/memcached_controller.go.tmpl pkg/controller/kubedge/kubedge_controller.go
vi pkg/controller/kubedge/kubedge_controller.go
```


## Building the kubedge-operator-go

``bash
# operator-sdk build kubedge/kubedge-operator-go-dev:v0.0.1
./manualbuild.sh 
```

## Deployment of kubedge-operator-go manually

```bash
kubectl create -f deploy/crds/arpscan_v1alpha1_kubedge_crd.yaml 
kubectl create -f deploy/operator/service_account.yaml 
kubectl create -f deploy/operator/role
kubectl create -f deploy/operator/role.yaml 
kubectl create -f deploy/operator/role_binding.yaml 
kubectl create -f deploy/operator/operator.yaml 
```

```bash
kubectl apply -f deploy/crds/arpscan_v1alpha1_kubedge_cr.yaml
```

```bash
kubectl get all

NAME                                    READY   STATUS    RESTARTS   AGE
pod/example-kubedge-5f9c4bfc95-hkbwk    1/1     Running   0          4m57s
pod/example-kubedge-5f9c4bfc95-qvctp    1/1     Running   0          4m57s
pod/example-kubedge-5f9c4bfc95-wjn9f    1/1     Running   0          4m57s
pod/kubedge-operator-5c89df997c-zclzh   1/1     Running   0          18m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   30h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/example-kubedge    3/3     3            3           4m57s
deployment.apps/kubedge-operator   1/1     1            1           18m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/example-kubedge-5f9c4bfc95    3         3         3       4m57s
replicaset.apps/kubedge-operator-5c89df997c   1         1         1       18m
```

```bash
kubectl get kubedge/example-kubedge -o yaml

apiVersion: arpscan.kubedge.cloud/v1alpha1
kind: Kubedge
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"arpscan.kubedge.cloud/v1alpha1","kind":"Kubedge","metadata":{"annotations":{},"name":"example-kubedge","namespace":"default"},"spec":{"size":3}}
  creationTimestamp: "2018-12-11T20:20:50Z"
  generation: 2
  name: example-kubedge
  namespace: default
  resourceVersion: "41966"
  selfLink: /apis/arpscan.kubedge.cloud/v1alpha1/namespaces/default/kubedges/example-kubedge
  uid: 40beeaa7-fd82-11e8-bdcd-0800272318b1
spec:
  size: 3
  string: ""
status:
  nodes:
  - example-kubedge-5f9c4bfc95-hkbwk
  - example-kubedge-5f9c4bfc95-qvctp
  - example-kubedge-5f9c4bfc95-wjn9f
```

## Deployment of kubedge-operator-go using Operator Life Cycle Manager

### Install the framework

Install the framework....run it twice
TODO: Use the helm chart instead

```bash
kubectl create -f deploy/upstream/manifests/latest/
kubectl create -f deploy/upstream/manifests/latest/
```

###  Deploy the lcm operator

```bash
kubectl create -f deploy/olm/crd.yaml
kubectl create -f deploy/olm/rbac.yaml
kubectl create -f deploy/olm/kubedgeoperator.0.0.1.csv.yaml
```
 

```bash
kubectl get all --all-namespaces

NAMESPACE     NAME                                           READY   STATUS    RESTARTS   AGE
default       pod/kubedge-app-operator-7f77fb5789-ztc7n      1/1     Running   0          4m56s
kube-system   pod/calico-etcd-l4s4r                          1/1     Running   1          36h
kube-system   pod/calico-kube-controllers-674c8478c7-qnglf   1/1     Running   1          36h
kube-system   pod/calico-node-jg227                          2/2     Running   4          36h
kube-system   pod/coredns-86c58d9df4-mfdf5                   1/1     Running   1          37h
kube-system   pod/coredns-86c58d9df4-nhk8w                   1/1     Running   1          37h
kube-system   pod/etcd-kubedgesdk                            1/1     Running   1          37h
kube-system   pod/kube-apiserver-kubedgesdk                  1/1     Running   1          37h
kube-system   pod/kube-controller-manager-kubedgesdk         1/1     Running   1          37h
kube-system   pod/kube-proxy-678j6                           1/1     Running   1          37h
kube-system   pod/kube-scheduler-kubedgesdk                  1/1     Running   1          37h
olm           pod/catalog-operator-8858cd9d9-vdgbg           1/1     Running   0          3h44m
olm           pod/olm-operator-855b85554c-6rr2w              1/1     Running   0          3h44m
olm           pod/package-server-5b77d98848-hmjl2            1/1     Running   0          3h42m

NAMESPACE     NAME                                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
default       service/kubernetes                          ClusterIP   10.96.0.1       <none>        443/TCP         37h
kube-system   service/calico-etcd                         ClusterIP   10.96.232.136   <none>        6666/TCP        36h
kube-system   service/kube-dns                            ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP   37h
olm           service/v1alpha1-packages-apps-redhat-com   ClusterIP   10.96.21.44     <none>        443/TCP         3h42m

NAMESPACE     NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                     AGE
kube-system   daemonset.apps/calico-etcd   1         1         1       1            1           node-role.kubernetes.io/master=   36h
kube-system   daemonset.apps/calico-node   1         1         1       1            1           beta.kubernetes.io/os=linux       36h
kube-system   daemonset.apps/kube-proxy    1         1         1       1            1           <none>                            37h

NAMESPACE     NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE
default       deployment.apps/kubedge-app-operator      1/1     1            1           4m56s
kube-system   deployment.apps/calico-kube-controllers   1/1     1            1           36h
kube-system   deployment.apps/coredns                   2/2     2            2           37h
olm           deployment.apps/catalog-operator          1/1     1            1           3h44m
olm           deployment.apps/olm-operator              1/1     1            1           3h44m
olm           deployment.apps/package-server            1/1     1            1           3h42m

NAMESPACE     NAME                                                 DESIRED   CURRENT   READY   AGE
default       replicaset.apps/kubedge-app-operator-7f77fb5789      1         1         1       4m56s
kube-system   replicaset.apps/calico-kube-controllers-674c8478c7   1         1         1       36h
kube-system   replicaset.apps/coredns-86c58d9df4                   2         2         2       37h
olm           replicaset.apps/catalog-operator-8858cd9d9           1         1         1       3h44m
olm           replicaset.apps/olm-operator-855b85554c              1         1         1       3h44m
olm           replicaset.apps/package-server-5b77d98848            1         1         1       3h42m

NAMESPACE   NAME                                                                DISPLAY               VERSION   REPLACES   PHASE
default     clusterserviceversion.operators.coreos.com/kubedgeoperator.v0.0.1   Kubedge Application   0.0.1                Succeeded
olm         clusterserviceversion.operators.coreos.com/packageserver.v8.0.0     Package Server        8.0.0                Succeeded

NAMESPACE   NAME                                              NAME                TYPE       PUBLISHER   AGE
olm         catalogsource.operators.coreos.com/rh-operators   Red Hat Operators   internal   Red Hat     3h
```
