# Example Deployments

```bash
kubectl create namespace ex-211
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: test-nginx
  template:
    metadata:
      labels:
        app: test-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.26
```

```bash
kubectl -n ex-211 apply -f your-file.yaml
```

```bash
# Check state with
watch kubectl -n ex-211 get po -o wide
```

```bash
kubectl -n ex-211 set image deployment/test-nginx nginx=nginx:1.27
```

```bash
kubectl delete namespace ex-211
```


# Example ReplicaSet

```bash
kubectl create namespace ex-212
```

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: test-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: test-nginx
  template:
    metadata:
      labels:
        app: test-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.27
```

```bash
kubectl -n ex-212 apply -f your-file.yaml
```

```bash
# Check state with
watch kubectl -n ex-212 get po -o wide
```

```bash
kubectl -n ex-212 scale --replicas=5 replicaset/test-nginx
```

```bash
kubectl delete namespace ex-212
```


# Example StatefulSet

## Prepare Cluster for local storage
```bash
sudo mkdir -p /mnt/volumes/pv1
sudo chmod 777 /mnt/volumes/pv1

cat << EOF | kubectl apply -f -
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv1
spec:
  capacity:
    storage: 10Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  local:
    path: /mnt/volumes/pv1
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - $HOSTNAME
EOF

```

## Example

```bash
kubectl create namespace ex-213
```

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-statefulset
spec:
  serviceName: "my-service"
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:1.27
        volumeMounts:
        - name: my-volume
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: my-volume
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi

```

```bash
kubectl -n ex-213 apply -f your-file.yaml
```

```bash
# Check state with
watch kubectl -n ex-213 get po -o wide
```

```bash
kubectl delete namespace ex-213
kubectl delete pv local-pv1
```


# Example DaemonSet

```bash
kubectl create namespace ex-214
```

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:1.27

```

```bash
kubectl -n ex-214 apply -f your-file.yaml
```

```bash
# Check state with
watch kubectl -n ex-214 get po -o wide
```

```bash
kubectl delete namespace ex-214
```


# Example Job

```bash
kubectl create namespace ex-215
```

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

```bash
kubectl -n ex-215 apply -f your-file.yaml
```

```bash
# Check state with
kubectl -n ex-215 describe job pi
kubectl -n ex-215 get job 
kubectl -n ex-215 get po
```

```bash
kubectl delete namespace ex-215
```


# Example CronJob

## Cron Notation

```text
# Example cron syntax for daily execution at night
0 0 * * *
```

## Exercise

```bash
kubectl create namespace ex-216
```

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: pi
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: perl:5.34.0
            command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
          restartPolicy: Never
      backoffLimit: 4
```

```bash
kubectl -n ex-216 apply -f your-file.yaml
```

```bash
# Check state with
kubectl -n ex-216 describe cronjob pi
kubectl -n ex-216 get cronjob 
kubectl -n ex-216 get job 
kubectl -n ex-216 get po
```

```bash
kubectl delete namespace ex-216
```

