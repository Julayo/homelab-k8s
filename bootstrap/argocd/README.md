# Bootstrap de ArgoCD

Pasos únicos para arrancar el loop GitOps en un clúster nuevo. Después de esto,
**todo se gobierna desde Git** — no se vuelve a hacer `kubectl apply` a mano.

```sh
# 1. Instalar ArgoCD (manifiesto oficial, canal stable)
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 2. Esperar a que ArgoCD esté listo
kubectl -n argocd rollout status deploy/argocd-server --timeout=180s

# 3. Entregarle el repo: aplicar la Application raíz (la única vez a mano)
kubectl apply -f bootstrap/argocd/root-app.yaml
```

A partir del paso 3, la raíz observa `apps/` y crea cada `Application`; cada una
reconcilia su carpeta de `manifests/`. Cambiar el clúster = `git push`.

## Acceder a la UI

```sh
# contraseña inicial del usuario admin
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath='{.data.password}' | base64 -d; echo

# exponer la UI en localhost:8080
kubectl -n argocd port-forward svc/argocd-server 8080:443
# -> https://localhost:8080  (usuario: admin)
```
