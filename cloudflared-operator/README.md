## Cloudflare Operator

This is currently not fleet'ed but I wanted to make it handy nonetheless.

## Installation
### 1. Install baseline because apparently helm isnt a thing here
```bash
kubectl apply -k https://github.com/adyanth/cloudflare-operator/config/default
```

### 2. Create Secret
```bash
kubectl -n cloudflare-operator-system create secret generic cloudflare-secrets \
  --from-literal CLOUDFLARE_API_TOKEN=<token> \
  --from-literal CLOUDFLARE_API_KEY=<api-key>

```

### 2b. Create Secret (better way)
```yaml
apiVersion: v1
data:
  CLOUDFLARE_API_KEY: <base64 api-key>
  CLOUDFLARE_API_TOKEN: <base64 api-token>
kind: Secret
metadata:
  name: cloudflare-secrets
  namespace: cloudflare-operator-system
type: Opaque
```

### 3. Finish Up
```bash
kubectl create -f ./clustertunnel.yaml -f deployment.yaml
```

## Troubleshooting
### **CNAMES not Resolving**:
The operator manually goes in and creates the CNAME records within CloudFlare DNS. In the event that everything creates successfully but you cannot route, you need to go in and manually remove the CNAME entries from the CF DNS-UI, then recreate the deployment. An old installation likely was unable to perform cleanup and you retained the stale entries.

### **Nothing is working**:
Chances are, you are trying to use a tunnel that already exists. Rather than iterate on a tunnelid, this operator wants a real deal name. Be sure that this name is unique across your tunnel creations.

```yaml
  # Either existingTunnel or newTunnel can be specified, not both
  newTunnel:
    name: new-tunnel
  existingTunnel:
    ## Existing Tunnel id/name to run on. Tunnel Name and Tunnel ID cannot be both empty. If both are provided, id is used if valid, else falls back to name
    id: <tunnel-id>
    name: existing-tunnel
```

### Testing a token
```bash
curl "https://api.cloudflare.com/client/v4/user/tokens/verify" \
     -H "Authorization: Bearer <token>"
```

### Wildcard DNS?
[YES](https://github.com/adyanth/cloudflare-operator/blob/main/docs/configuration.md#tunnelbinding)