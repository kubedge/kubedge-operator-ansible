# operator-framework usage for kubedge

## Initialization of kubedge-operator-ansible

```bash
operator-sdk new kubedge-operator-ansible --api-version=arpscan.kubedge.cloud/v1alpha1 --kind=Kubedge --type=ansible
cd kubedge-operator-ansible
git remote add origin git@github.com:kubedge/kubedge-operator-ansible
git push -u origin master
```

## Coding the kubedge-operator-ansible

```bash
vi build/Dockerfile
vi watches.yaml
vi roles/Kubedge/tasks/main.yml
```


## Building the kubedge-operator-ansible

``bash
./manualbuild.sh 
```

## Deployment of kubedge-operator-ansible manually

```bash
kubectl create -f deploy/crds/arpscan_v1alpha1_kubedge_crd.yaml 
kubectl create -f deploy/operator/service_account.yaml 
kubectl create -f deploy/operator/role.yaml 
kubectl create -f deploy/operator/role_binding.yaml 

```

```bash
kubectl apply -f deploy/crds/arpscan_v1alpha1_kubedge_cr.yaml
```

```bash
kubectl get all

NAME                                            READY   STATUS    RESTARTS   AGE
pod/example-kubedge-kubedge-68b49cbdfb-nrxhk    1/1     Running   0          6m
pod/kubedge-operator-ansible-7d65676ff5-bzsl5   1/1     Running   0          8m37s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   166m

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/example-kubedge-kubedge    1/1     1            1           6m
deployment.apps/kubedge-operator-ansible   1/1     1            1           8m37s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/example-kubedge-kubedge-68b49cbdfb    1         1         1       6m
replicaset.apps/kubedge-operator-ansible-7d65676ff5   1         1         1       8m37s
```

## Deployment of kubedge-operator-ansible using Operator Life Cycle Manager

### Install the framework

Install the framework....run it twice
TODO: Use the helm chart instead

```bash
kubectl create -f deploy/upstream/manifests/latest/
kubectl create -f deploy/upstream/manifests/latest/
```

###  Deploy the olm operator

***TBD***
