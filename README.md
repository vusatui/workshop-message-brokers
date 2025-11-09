# Workshop Message Brokers


![Taskfile](https://img.shields.io/badge/taskfile-07C160?style=flat&logo=taskfile&logoColor=green)
![Docker](https://img.shields.io/badge/docker-%232496ED.svg?style=flat&logo=docker&logoColor=white)
![minikube](https://img.shields.io/badge/minikube-%23316CE6.svg?style=flat&logo=kubernetes&logoColor=white)

This workshop explores the core challenges of reliable communication in distributed systems by focusing on messaging platform delivery semantics. We'll move beyond simple database-driven architectures to understand why and when a dedicated messaging system becomes essential for building scalable and resilient applications.

Throughout this workshop, we are trying to answer the following questions:

  * Why not just use a DB? 

  * What if we just 'fire and forget'? 

  * How do we guarantee it arrives?

  * We solved reliability, but what other problem can appear?

  * How do we make consumers 'safe' for duplicates?

  * Can the system just handle this for me?

By the end, you'll have a better understanding when and how start thinking about delivery guarantees for your specific needs.


## Prerequisites

1. Install [docker](https://docs.docker.com/engine/install/)
2. Install [taskfile](https://taskfile.dev/docs/installation)
3. Install [minikube](https://minikube.sigs.k8s.io/docs/start)
4. Install [helm](https://helm.sh/docs/intro/install/)
5. Install [Kustomize](https://kubectl.docs.kubernetes.io/installation/kustomize/)


## Quickstart
  
1. To manage the cluster:
   ```bash
    task cluster.start
   ```
2. Configure the local DNS resolver to work with the cluster's ingress. This allows you to access services using `.test` domains (e.g., `http://my-app.test`).
   > **Note:** This command requires `sudo` privileges to modify system network settings.
    ```bash
    task dns.add
    ```

3. Run the core services, ingress and monitoring which will be required throughout the course
  ```bash
  task core:deploy
  ```

4. Access the Headlamp UI:
   - Get your authentication token by running:
     ```bash
     task core:headlamp.token
     ```
   - Open your browser and navigate to **http://headlamp.test**
   - Paste the token into the login field to access the dashboard.

> **Note:** If you are using minikube with the Docker driver on macOS, you need to run the command `task cluster.tunnel` to access the services in the container.

## Quickend

1. To stop the cluster:
    ```bash
    task cluster.stop
    ``` 

## Monitoring (Helm, official chart)

We follow the official `kube-prometheus-stack` chart documentation (see Artifact Hub: https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack).

1. Check status and ingress endpoints:
   ```bash
   task core:monitoring.status
   ```
2. Open UIs:
   - Prometheus: http://prometheus.test
   - Grafana: http://grafana.test (user: admin, password: admin)


> Note: On macOS with Docker driver use `task cluster.tunnel` to expose services.

Troubleshooting:
- If you previously installed monitoring via another method and see Helm ownership errors, run a clean uninstall and CRD cleanup, then install again:
  ```bash
  task core:rollback
  task core:deploy
  ```

### Hosts entries for ingress

Add local DNS entries to `/etc/hosts` for ingress endpoints (requires sudo):
```bash
task dns.add
```
Remove them:
```bash
task dns.remove
```

Note: Vendored chart files are not committed. The folder `k8s/monitoring/charts/` is ignored via `.gitignore`. Use the Helm repo with a pinned `--version`.

### How usecases add monitoring

Usecases are independent apps that expose metrics and provide their own `ServiceMonitor`/`PodMonitor` via Kustomize. Prometheus is configured to automatically discover monitors across all namespaces.

Minimal example (inside a usecase repo folder):

```yaml
# usecases/<name>/k8s/base/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app
  namespace: my-ns
  labels:
    app: my-app
spec:
  selector:
    app: my-app
  ports:
    - name: metrics
      port: 8080
      targetPort: 8080
```

```yaml
# usecases/<name>/k8s/base/servicemonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-app
  namespace: my-ns
spec:
  selector:
    matchLabels:
      app: my-app
  endpoints:
    - port: metrics
      path: /metrics
      interval: 15s
```

```yaml
# usecases/<name>/k8s/base/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: my-ns
resources:
  - service.yaml
  - servicemonitor.yaml
  # + your Deployment/StatefulSet/etc.
```

Apply the usecase (after creating its namespace):
```bash
kubectl create ns my-ns --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -k usecases/<name>/k8s/base
```

Prometheus will discover the target automatically (thanks to open selectors in monitoring values).