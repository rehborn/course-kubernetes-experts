# Extend Kubernetes

```yaml
# Common Pod header
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
spec:
    # pod spec
```


```yaml
# Custom Resource
apiVersion: your.namespace.org/v1
kind: CustomPet
metadata:
  name: pet-1
spec:
    # custom spec
```
