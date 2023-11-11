# Synology-CSI Fleet Bundle

## Synology client secret
This is required but not created in this bundle. I apply the secret manually or through a private fleet-bundle repo.

#### `client-secret.yaml`
```yaml
---
clients:
  - host: nas1.lark.lol
    port: 5001
    https: true
    username: sanuser
    password: <>
```
### Creating the secret
```
apiVersion: v1
kind: Secret
metadata:
  name: synology-creds
  namespace: synology-csi
type: Opaque
stringData:
  client-info.yaml: |
    clients:
      - host: nas1-10g.lark.lol
        port: 5001
        https: true
        username: sanuser
        password: <>   
```

#### example.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: /mnt/data

---

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: standard

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: nginx
        volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: storage
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: example-pvc

```
