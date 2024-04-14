# Kubernetes Helm charts for backend API microservice with Consul
## Getting started

1. Install helm
```bash
$ curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

2. Add HashiCorp Consul repo
```bash
$ helm repo add hashicorp https://helm.releases.hashicorp.com
```

3. Install consul with Kubernetes Gateway API
- `gateway.yaml`
```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: consul-gateway
  namespace: gateway-namespace
spec:
  gatewayClassName: consul
  listeners:
  - protocol: HTTP
    port: 16000
    name: http
    allowedRoutes:
      namespaces:
        from: All

```
- Consul's `values.yaml`
```yaml
# Configure global settings in this section.
global:
  enabled: true
  name: consul
  datacenter: dc1
  # Bootstrap ACLs within Consul. This is highly recommended.
  acls:
    manageSystemACLs: true

# Configure your Consul servers in this section.
server:
  enabled: true
  replicas: 1
  resources:
    requests:
      memory: "200Mi"
      cpu: "100m"
    limits:
      memory: '8Gi'
      cpu: '2'

# Enable and configure the Consul UI.
ui:
  enabled: true
  service:
    type: "LoadBalancer"

# Enable Consul connect pod injection
connectInject:
  default: true
  resources:
    requests:
      memory: "500Mi"
      cpu: "250m"
    limits:
      memory: "500Mi"
      cpu: "250m"
  failurePolicy: "Ignore"
  initContainer:
    resources:
      requests:
        memory: "150Mi"
        cpu: "250m"
      limits:
        memory: "150Mi"
        cpu: 500m
```
- Install
```bash
$ helm install consul hashicorp/consul --create-namespace --namespace consul -f path/to/consul/values.yaml
$ kubectl apply -f path/to/gateway.yaml
```

4. Test and try
```bash
$ helm install api-charts . --create-namespace --namespace api
```
