# homelab-k8s

GitOps para mi clúster de homelab: un nodo **k3s** gobernado por **ArgoCD** desde este
repositorio. El estado deseado del clúster vive en Git; ArgoCD lo reconcilia. No se aplica
nada con `kubectl apply` a mano — se hace `git push`.

Prueba tangible de Platform Engineering + práctica hacia CKA.

## Nodo

- Host: desktop `cachyos-x8664`
- k3s single-node (server + agent en la misma máquina)
- Contenedores en containerd propio de k3s (`/var/lib/rancher/k3s`)

## Estructura

```
bootstrap/argocd/   # instalación de ArgoCD y la Application raíz (app-of-apps)
apps/               # una Application de ArgoCD por servicio (lo que se despliega)
manifests/          # los manifiestos Kubernetes de cada servicio
```

Flujo: un `Application` raíz observa `apps/`; cada archivo ahí es otra `Application`
que apunta a una carpeta de `manifests/`. Añadir un servicio = commitear su manifiesto
y su `Application`, y push.

## Fases

- [x] **0** — prep: repo + nodo listo
- [ ] **1** — k3s single-node (`kubectl get nodes` = Ready)
- [ ] **2** — ArgoCD + primer loop GitOps (podinfo desde Git)
- [ ] **3** — migrar un servicio real (uptime-kuma → PVC + Ingress)
- [ ] **4** — endurecer (sealed-secrets) + documentar

## Reglas

- **Cero secretos en el repo.** Los secretos van cifrados (sealed-secrets, Fase 4).
- Solo manifiestos declarativos.
