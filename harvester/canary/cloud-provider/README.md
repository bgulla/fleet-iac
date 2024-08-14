# Harvester Cloud Provider Plugin 

## `LoadBalancer` Annotation needed for `kube-vip`

LoadBalancer stuck in provisioning? here's the annotation you're looking for!

```yaml
annotations:
  cloudprovider.harvesterhci.io/ipam: dhcp
```
