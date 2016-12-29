####1st of all, install glusterfs-client on all nodes

####2nd (https://github.com/heketi/heketi/tree/master/extras/kubernetes)

label nodes storagenode=glusterfs  

kubectl create -f gluster-daemonset.json  
kubectl create -f deploy-heketi-deployment.json  

####3st (https://github.com/heketi/heketi/wiki/Kubernetes-Integration)

kubectl port-forward deploy-heketi-1211581626-2jotm :8080  
curl http://localhost:57598/hello  
export HEKETI_CLI_SERVER=http://localhost:57598  

####4th
create topology  
heketi-client/bin/heketi-cli topology load --json=topology-sample.json  

####5th
provision heketi volume  
heketi-client/bin/heketi-cli setup-openshift-heketi-storage  

it will produce  
heketi-storage.json  

kubectl create -f heketi-storage.json  

cleanup after deploy  
kubectl delete all,service,jobs,deployment,secret --selector="deploy-heketi"  


####6th
create deployment heketi  

kubectl create -f heketi-deployment.json  
kubectl create -f heketi-svc.yaml  

####7th
create storage-class  
use ip of service heketi eg 10.46.0.4  
kubectl create -f storage-heketi.yaml    

####8th

create PersistentVolumeClaim like in dir examples  
remember  to use  

```
annotations:
  volume.beta.kubernetes.io/storage-class: gluster-heketi
```  
