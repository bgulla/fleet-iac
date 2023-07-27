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