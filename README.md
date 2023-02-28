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
