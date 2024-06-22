# Deployments

## Example

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

