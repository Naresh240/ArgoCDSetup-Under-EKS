# argocd setup with helm

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd argo/argo-cd \
  --namespace argocd --create-namespace \
  --set redis-ha.enabled=false
```

## Do port forwarding

```bash
kubectl port-forward service/argocd-server -n argocd 8080:443
```

## To login we need credentials

```bash
username: admin

## Get password using
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Deploy application to sync with github repo

```yaml
kubectl apply -f argocd_app.yaml
```

## If need to deploy applications using helm charts add application files under applications directory