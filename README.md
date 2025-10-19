# Workshop Message Brokers


![Taskfile](https://img.shields.io/badge/taskfile-07C160?style=flat&logo=taskfile&logoColor=green)
![Docker](https://img.shields.io/badge/docker-%232496ED.svg?style=flat&logo=docker&logoColor=white)
![minikube](https://img.shields.io/badge/minikube-%23316CE6.svg?style=flat&logo=kubernetes&logoColor=white)


## Prerequisites

1. Instal [docker](https://docs.docker.com/engine/install/)
2. Install [taskfile](https://taskfile.dev/docs/installation)
3. Install [minikube](https://minikube.sigs.k8s.io/docs/start)
4. Install [helm](https://helm.sh/docs/intro/install/)


## Quickstart
  
1. Start the cluster and deploy Headlamp Ingress:
   ```bash
   task --taskfile minikube.yml
   ```
   This starts Minikube, enables addons, deploys the Headlamp Ingress, and configures DNS once.

2. Start the Minikube tunnel (required to access Ingress on macOS/Linux):
   - Keep it running in a separate terminal; it may ask for sudo to bind ports 80/443.
   ```bash
   sudo -E minikube tunnel -p workshop-mb
   ```

3. Configure local DNS for `.test` domains (re-run after starting the tunnel):
   > Requires `sudo` to edit `/etc/hosts`.
   ```bash
   task --taskfile minikube.yml configure.ingress-dns
   ```

4. Get a Headlamp token and open the UI:
   ```bash
   task --taskfile minikube.yml get.headlamp-token
   open http://headlamp.test/
   ```
   Paste the token into the login field.


## Quickend

1. To stop the cluster:
    ```bash
    task --taskfile minikube.yml cluster.stop
    ``` 