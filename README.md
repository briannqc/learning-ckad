# Learning CKAD
Taking course: https://trainingportal.linuxfoundation.org/courses/kubernetes-for-developers-lfd259

## Cheat Sheet

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
