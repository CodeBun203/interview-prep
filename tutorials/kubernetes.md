# Tutorial: Kubernetes (via k3s on your own server)

> Goal: migrate 2–3 real services from Docker Compose to actual Kubernetes so "I run K8s" is true.
> Use **k3s** — a fully-conformant, lightweight K8s ideal for a single node. This is the #1 probed
> gap on your resume; closing it for real is worth more than any other single item.

## Mental model (Compose → K8s translation)
| Compose concept | Kubernetes concept |
|---|---|
| `service` | **Deployment** (manages **Pods** via a **ReplicaSet**) |
| container | **Pod** (1+ containers; smallest deployable unit) |
| `ports:` | **Service** (stable virtual IP/DNS in front of Pods) |
| public URL / proxy | **Ingress** (HTTP routing into the cluster) |
| `environment:` | **ConfigMap** (non-secret) / **Secret** |
| `volumes:` | **PersistentVolumeClaim (PVC)** → PersistentVolume |
| `restart: always` | controller **reconciliation** (self-healing) |
| stateful db | **StatefulSet** + PVC (stable identity/storage) |

K8s is **declarative**: you submit desired state (YAML); the **control plane** continuously
**reconciles** reality toward it. That reconciliation loop *is* the headline concept — say it.

## Step 1 — install k3s
```bash
curl -sfL https://get.k3s.io | sh -          # installs server + kubectl + containerd
sudo k3s kubectl get nodes                    # one Ready node
mkdir -p ~/.kube && sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config && sudo chown $USER ~/.kube/config
kubectl get pods -A
```

## Step 2 — a Deployment + Service (start with the stateless Portal)
`k8s/portal.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: portal }
spec:
  replicas: 2                       # two Pods → rolling updates, no downtime
  selector: { matchLabels: { app: portal } }
  template:
    metadata: { labels: { app: portal } }
    spec:
      containers:
        - name: portal
          image: washgen-portal:latest
          ports: [{ containerPort: 3000 }]
          envFrom:
            - configMapRef: { name: portal-config }
            - secretRef:    { name: portal-secrets }
          resources:                # requests=guaranteed, limits=ceiling
            requests: { cpu: "100m", memory: "128Mi" }
            limits:   { cpu: "500m", memory: "512Mi" }
          readinessProbe:           # don't send traffic until ready
            httpGet: { path: /, port: 3000 }
            initialDelaySeconds: 5
          livenessProbe:            # restart if it wedges
            httpGet: { path: /, port: 3000 }
            initialDelaySeconds: 15
---
apiVersion: v1
kind: Service
metadata: { name: portal }
spec:
  selector: { app: portal }
  ports: [{ port: 80, targetPort: 3000 }]
  type: ClusterIP                   # internal; Ingress exposes it externally
```

## Step 3 — config & secrets
```bash
kubectl create configmap portal-config --from-literal=NODE_ENV=production
kubectl create secret generic portal-secrets \
  --from-literal=AUTH_KEYCLOAK_SECRET=... --from-literal=AUTH_KEYCLOAK_ID=...
```

## Step 4 — Ingress (route the tunnel into the cluster)
k3s ships **Traefik** as the default ingress controller:
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata: { name: portal }
spec:
  rules:
    - host: app.washingtonelectric.info
      http:
        paths:
          - path: /
            pathType: Prefix
            backend: { service: { name: portal, port: { number: 80 } } }
```
Point `cloudflared` at the Traefik ingress instead of the raw container. Now traffic flows
Cloudflare → Ingress → Service → Pod.

## Step 5 — stateful workload (Postgres, to prove you get state)
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata: { name: postgres }
spec:
  serviceName: postgres
  replicas: 1
  selector: { matchLabels: { app: postgres } }
  template:
    metadata: { labels: { app: postgres } }
    spec:
      containers:
        - name: postgres
          image: postgres:16-alpine
          envFrom: [{ secretRef: { name: pg-secrets } }]
          volumeMounts: [{ name: data, mountPath: /var/lib/postgresql/data }]
  volumeClaimTemplates:
    - metadata: { name: data }
      spec:
        accessModes: ["ReadWriteOnce"]
        resources: { requests: { storage: 10Gi } }
```
Talking point: "Stateless apps as Deployments; stateful data as StatefulSets with PVCs so storage
identity is stable across reschedules."

## Step 6 — the kubectl commands you must know cold
```bash
kubectl apply -f k8s/                 # declaratively create/update everything
kubectl get pods -o wide              # what's running, where
kubectl describe pod <name>           # events/why it's not starting
kubectl logs -f deploy/portal         # stream logs
kubectl exec -it <pod> -- sh          # shell into a container
kubectl rollout status deploy/portal  # watch a rolling update
kubectl rollout undo deploy/portal    # roll back a bad deploy
kubectl scale deploy/portal --replicas=3
kubectl get events --sort-by=.lastTimestamp
```

## Step 7 — Helm (package it — optional but impressive)
**Helm** = the K8s package manager; templatize the YAML into a **chart** with `values.yaml`:
```bash
helm create portal-chart      # scaffold
helm install portal ./portal-chart -f values.yaml
helm upgrade portal ./portal-chart --set image.tag=v2
```
Say: "I package services as Helm charts so config is values-driven across environments."

## Testing in the K8s world
- **App tests** still run in CI (Pytest/Jest) before the image is built.
- **Manifest validation:** `kubectl apply --dry-run=server`, **`kubeval`**/**`kubeconform`** (schema),
  **`helm lint`**, **`helm template`** (render & inspect).
- **Policy tests:** **OPA/Gatekeeper**, **Kyverno** ("no privileged pods", "limits required").
- **Integration:** **`kind`** (Kubernetes-in-Docker) spins an ephemeral cluster in CI; deploy and
  smoke-test against it.

## Common tools / ecosystem to name
| Need | Tool |
|---|---|
| Lightweight cluster | **k3s**, **kind**, **minikube** |
| Package/template | **Helm**, Kustomize |
| Ingress controller | Traefik (k3s default), NGINX Ingress |
| GitOps deploy | **Argo CD**, Flux |
| Secrets | Sealed Secrets, External Secrets, Vault |
| Autoscaling | **HPA** (Horizontal Pod Autoscaler) |
| Observability | Prometheus + Grafana (you already run these!) via kube-prometheus-stack |

## Interview soundbites
- "I migrated the stateless tier to k3s — Deployments with readiness/liveness probes and resource
  requests/limits, Services + Traefik Ingress, ConfigMaps/Secrets, and StatefulSets+PVCs for
  Postgres. Cloudflare Tunnel terminates into the ingress."
- The headline concept: "**K8s is a declarative reconciliation loop** — I describe desired state, the
  control plane self-heals toward it." That one sentence signals real understanding.
- Honest bridge (before you finish): "In production today I orchestrate with Compose on a single
  node; I'm running the same services on k3s to get rolling updates, self-healing, and HPA." Then
  finish the migration and upgrade the verb to "I run."
- Know cold: **Pod, Deployment, ReplicaSet, Service, Ingress, ConfigMap, Secret, PV/PVC,
  StatefulSet, namespace, probe, requests/limits, HPA, control plane, kubelet, reconciliation,
  rolling update, Helm chart.**
