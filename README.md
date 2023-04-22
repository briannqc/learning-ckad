# Learning CKAD
Taking course: https://trainingportal.linuxfoundation.org/courses/kubernetes-for-developers-lfd259

## Curriculum

https://github.com/cncf/curriculum

## Cheat Sheet

### Pod

```shell
# Run a pod without yaml
$ k run ubuntu-pod --image=ubuntu --command -- sleep 86400
pod/ubuntu-pod created

# Get all pods from current namespace
$ kgp

# Get all pods from a specific namespace
$ kgp -n specific

# Get all pods from all namespaces
$ kgpa

# Get a pod with name
$ kgp mypod

# Bash a pod
$ keti mypod -- bash
```

### Job

Sample: job.yaml

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sleepy
spec:
  template:
    spec:
      containers:
        - name: resting
          image: busybox
          command: ["/bin/sleep"]
          args: ["3"]
      restartPolicy: Never
```

```shell
$ k create -f job.yaml
job.batch/sleepy created

$ k apply -f job.yaml
job.batch/sleepy created

$ kgj

$ kdj sleepy

$ kgj sleepy -o yaml

$ kgp

$ k delete -f job.yaml
job.batch "sleepy" deleted

$ k delete job sleepy
job.batch "sleepy" deleted
```

### CronJob

Sample: cronjob.yaml

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: sleepy
spec:
  schedule: "*/2 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: resting
              image: busybox
              command: [/bin/sleep"]
              args: ["3"]
          restartPolicy: Never
```

```shell
$ k create -f cronjob.yaml
cronjob.batch/sleepy created

$ k delete cronjob sleepy
cronjob.batch "sleepy" deleted

$ k apply -f job.yaml
cronjob.batch/sleepy created

$ kgcj
NAME     SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
sleepy   */2 * * * *   False     0        <none>          26s

$ kdcj sleepy

$ kgcj sleepy -o yaml

$ kgj
NAME              COMPLETIONS   DURATION   AGE
sleepy-27959136   0/1           3s         3s

$ kgp

$ k delete -f cronjob.yaml
cronjob.batch "sleepy" deleted
```

### Custom Resource Definitions (CRDs)

```shell
# Get available CRDs
$ k get crd

# Get sparkapplication CRD objects
$ k get sparkapplication

# Get a specific sparkapplication CRD object with name: my-spark-app
$ k get sparkapplication my-spark-app

# Get flinkcluster CRD objects
$ k get flinkcluster
```

### Labels

```shell
# Create a deployment with name "design2" using "nginx" image
$ k create deployment design2 --image=nginx
deployment.apps/design2 created

# Get deployments with SELECTOR: app=design2
$ k get deployments.apps design2 -o wide
NAME      READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES   SELECTOR
design2   1/1     1            1           49s   nginx        nginx    app=design2

# Get pods with SELECTOR: app=design2
$ kgp -l app=design2
NAME                       READY   STATUS    RESTARTS   AGE
design2-7854558bbc-95r9q   1/1     Running   0          3m14s

# Delete pods with label SELECTOR: app=design2
$ k delete pod -l app=design2
pod "design2-7854558bbc-95r9q" deleted
```

### Rolling Updates and Rollbacks

```shell
# Create a deployment from yaml file
$ k apply -f deploy.yaml

# Edit a deployment, e.g. Image
$ ked nginx

# View the update history of the deployment
$ k rollout history deployment nginx
deployment.apps/nginx 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>

# Compare the output of therollout historyfor the two revisions
$ k rollout history deployment nginx --revision=1 > one.out
$ k rollout history deployment nginx --revision=2 > two.out
$ diff one.out two.out
1c1
< deployment.apps/nginx with revision #1
---
> deployment.apps/nginx with revision #2
4c4
<       pod-template-hash=5d6d94b454
---
>       pod-template-hash=747b8c9d84
7c7
<     Image:    nginx:3.10
---
>     Image:    nginx:1.16.0

# View what would be undone using the --dry-run option while undoing the rollout.
$ k rollout undo --dry-run=client deployment/nginx

# Rollout
$ k rollout undo deployment nginx --to-revision=1
```

### ServiceAccount  and ClusterRole

```shell
$ k get clusterroles
NAME                                                                   CREATED AT
admin                                                                  2023-04-15T23:08:26Z
cluster-admin                                                          2023-04-15T23:08:26Z
edit                                                                   2023-04-15T23:08:26Z
kindnet                                                                2023-04-15T23:08:28Z
...

$ k get clusterroles cluster-admin -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: "2023-04-15T23:08:26Z"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: cluster-admin
  resourceVersion: "72"
  uid: da2ff982-d9a7-4547-a691-73efe0e54a87
rules:
  - apiGroups:
      - '*'
    resources:
      - '*'
    verbs:
      - '*'
  - nonResourceURLs:
      - '*'
    verbs:
      - '*'

$ k get clusterroles admin -o yaml
aggregationRule:
  clusterRoleSelectors:
    - matchLabels:
        rbac.authorization.k8s.io/aggregate-to-admin: "true"
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
  creationTimestamp: "2023-04-15T23:08:26Z"
  labels:
    kubernetes.io/bootstrapping: rbac-defaults
  name: admin
  resourceVersion: "350"
  uid: e9a5da5b-2439-4d59-a979-758832a78bc3
rules:
  - apiGroups:
      - ""
    resources:
      - pods/attach
      - pods/exec
      - pods/portforward
      - pods/proxy
      - secrets
      - services/proxy
    verbs:
      - get
      - list
      - watch
```

```
$ k apply -f clusterrole.yaml
clusterrole.rbac.authorization.k8s.io/secret-access-cr created

$ k apply -f serviceaccount.yaml
serviceaccount/secret-access-sa created

$ k apply -f rolebinding.yaml
rolebinding.rbac.authorization.k8s.io/secret-rb created
```

### Services

```shell
# Get all services in current namespace
$ kgs
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6d9h

# Get all services in a specific namespace
$ kgs -n kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   6d9h

# Get all services from all namespaces
$ kgsa
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  6d9h
kube-system   kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   6d9h

# Get a service
$ kgs my-app -o yaml

# Delete a service
$ k delete service my-app

# Create a new service without defining yaml
$ k expose deployment mainapp --name=shopping --type=NodePort --port=80
```
