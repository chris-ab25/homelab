## 🚀 ArgoCD

Follow the steps below to install and configure ArgoCD on your system.

### 1. Add argo helm repository

Use helm command.

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
```

### 2. Create values.yaml

We will use values.yaml to override some defaults based on our setup. First, we’ll set our domain using sslip.io to enable DNS resolution. We’ll also disable ingress since we already have Traefik set up. Finally, we’ll define CPU and memory requests as safeguards to prevent overloading our machine.

```yaml
global:
  domain: argocd.192.168.18.99.sslip.io 

server:
  ingress:
    enabled: false

  resources:
    limits:
      cpu: 500m
      memory: 512Mi
    requests:
      cpu: 100m
      memory: 256Mi
```

### 3. Install the chart
Use helm command.

```bash
helm install argocd argo/argo-cd \
  --namespace argocd \
  --create-namespace \
  -f argocd-values.yaml
```

Helm install output:
```bash
[root@fedora argocd]# helm install argocd argo/argo-cd \
  --namespace argocd \
  --create-namespace \
  -f values.yaml

NAME: argocd
LAST DEPLOYED: Sat May  2 16:49:07 2026
NAMESPACE: argocd
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
TEST SUITE: None
NOTES:
In order to access the server UI you have the following options:

1. kubectl port-forward service/argocd-server -n argocd 8080:443

    and then open the browser on http://localhost:8080 and accept the certificate

2. enable ingress in the values file `server.ingress.enabled` and either
      - Add the annotation for ssl passthrough: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-1-ssl-passthrough
      - Set the `configs.params."server.insecure"` in the values file and terminate SSL at your ingress: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-2-multiple-ingress-objects-and-hosts


After reaching the UI the first time you can login with username: admin and the random password generated during the installation. You can find the password by running:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

(You should delete the initial secret afterwards as suggested by the Getting Started Guide: https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)
```

### 4. Create ingressroute.yaml
We need to setup ingressroute for argocd as K3s is using Traefik.

```yaml
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: argocd-ingressroute
  namespace: argocd
spec:
  entryPoints:
    - websecure
  routes:
  - kind: Rule
    match: Host(`argocd.192.168.18.99.sslip.io`)
    services:
    - name: argocd-server
      port: 443
      serversTransport: skip-verify
```

## 5. Create serverstransport.yaml
Since we’re still in the lab and haven't set up certs yet, I'm adding a 'skip TLS verify' rule to the Traefik config for now.

```yaml
apiVersion: traefik.io/v1alpha1
kind: ServersTransport
metadata:
  name: skip-verify
  namespace: argocd
spec:
  insecureSkipVerify: true
```

## 6. Deploy ingressroute.yaml and serverstransport.yaml
Use kubectl command

```bash
kubectl apply -f serverstransport.yaml
kubectl apply -f ingressroute.yaml
```

## 7. Verify creation of both resource
Use kubectl command on argocd namespace.

```bash
[root@fedora argocd]# kubectl -n argocd get serverstransport
NAME          AGE
skip-verify   7m24s
[root@fedora argocd]# kubectl -n argocd get ingressroute
NAME                  AGE
argocd-ingressroute   18h
[root@fedora argocd]#
```

## 8. Verify Argocd url - argocd.192.168.18.99.sslip.io
<img width="1914" height="1031" alt="image" src="https://github.com/user-attachments/assets/287c10fe-c920-45a5-af4e-b75dd10f40d4" />
