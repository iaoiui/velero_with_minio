# velero_with_minio
Kubernetes backup &amp; restore sample with Velero &amp; Minio

# Quick Start

```sh
$ kind create cluster

$ sh quickstat.sh
$ kubectl get po -n velero
```

# Backup Test

```sh
$ sh 10_deploy_sampleapp_with_pv.sh 
$ kubectl get po -n nginx-example

$ sh 20_backup.sh 
Backup request "nginx-backup" submitted successfully.
Run `velero backup describe nginx-backup` or `velero backup logs nginx-backup` for more details.

$ sh 21_simulate_disaster.sh 
namespace "nginx-example" deleted

$ kubectl get ns
NAME              STATUS   AGE
default           Active   4m4s
kube-node-lease   Active   4m5s
kube-public       Active   4m6s
kube-system       Active   4m6s
velero            Active   3m51s

$ sh 22_restore.sh 
Restore request "nginx-backup-20200308174714" submitted successfully.
Run `velero restore describe nginx-backup-20200308174714` or `velero restore logs nginx-backup-20200308174714` for more details.

$ kubectl get ns
NAME              STATUS   AGE
default           Active   4m39s
kube-node-lease   Active   4m40s
kube-public       Active   4m41s
kube-system       Active   4m41s
nginx-example     Active   11s
velero            Active   4m26s

$ sh 23_verify.sh 
NAME                                   READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-f96b7fd86-6kjll   2/2     Running   0          79s


NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/my-nginx   LoadBalancer   10.107.174.129   <pending>     80:31258/TCP   79s


NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   1/1     1            1           79s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-f96b7fd86   1         1         1       79s




NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-5e9d15c6-8296-41fd-8f5c-4597b720c6cc   50Mi       RWO            Delete           Bound    nginx-example/nginx-logs   standard                79s
```

# Cleanup

```sh
$ sh 99_uninstall_velero.sh 
namespace "velero" deleted
clusterrolebinding.rbac.authorization.k8s.io "velero" deleted
customresourcedefinition.apiextensions.k8s.io "backups.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "backupstoragelocations.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "deletebackuprequests.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "downloadrequests.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "podvolumebackups.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "podvolumerestores.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "resticrepositories.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "restores.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "schedules.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "serverstatusrequests.velero.io" deleted
customresourcedefinition.apiextensions.k8s.io "volumesnapshotlocations.velero.io" deleted

$ kind delete cluster
```
