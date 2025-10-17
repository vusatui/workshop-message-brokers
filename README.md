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
  
1. To start the cluster:
   ```bash
    task --taskfile minikube.yml cluster.start
   ```
2. Configure the local DNS resolver to work with the cluster's ingress. This allows you to access services using `.test` domains (e.g., `http://my-app.test`).
   > **Note:** This command requires `sudo` privileges to modify system network settings.
    ```bash
    task --taskfile minikube.yml configure.ingress-dns
    ```

3. Access the Headlamp UI:
   - Get your authentication token by running:
     ```bash
     task --taskfile minikube.yml get.headlamp-token
     ```
   - Open your browser and navigate to **http://headlamp.test**
   - Paste the token into the login field to access the dashboard.


## Quickend

1. To stop the cluster:
    ```bash
    task --taskfile minikube.yml cluster.stop
    ``` 