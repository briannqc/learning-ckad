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