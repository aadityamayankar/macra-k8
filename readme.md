# Kubernetes Deployment for Macra

This repository contains Kubernetes manifests for deploying infrastructure components and spring services for the Macra application.

## Project Structure

## Components

### Applications

- **authn**: Authentication service
- **authz**: Authorization service
- **eventsync**: Event synchronization service
- **opsadmin**: Operational admin service
- **user**: User service
- **utility-client**: Utility client for deployment

These are the containerized Spring Boot microservices. Each service has its own deployment manifest and service manifest. 
Docker images for these services are published to Docker Hub and can be pulled from the following repositories:
- [authn](https://hub.docker.com/r/mayankar/macra-authn)
- [authz](https://hub.docker.com/r/mayankar/macra-authz)
- [eventsync](https://hub.docker.com/r/mayankar/macra-eventsync)
- [opsadmin](https://hub.docker.com/r/mayankar/macra-opsadmin)
- [user](https://hub.docker.com/r/mayankar/macra-user)

The `utility-client` service is a utility client for testing and debugging purposes. 

### Infrastructure

- **PostgreSQL**: Database service
- **RabbitMQ**: Message broker service
- **Redis**: In-memory data structure store

### Cert-Manager

- **ClusterIssuer**: LetsEncrypt ClusterIssuer configuration
- **Certificate**: TLS certificate for `api.macra.live`

### Ingress

- **Ingress**: NGINX Ingress controller configuration for routing traffic to services

### ReplicaSets and StatefulSets

#### ReplicaSets

ReplicaSets ensure that we have 2 pod replicas are running at any given time. They are used for stateless applications where each instance is identical and can be replaced without any impact on the overall application state. In this project, we use ReplicaSets for the following services:

- **authn**
- **authz**
- **eventsync**
- **opsadmin**
- **user**

These services are stateless and can be scaled horizontally by simply adding more replicas. The ReplicaSet controller will ensures that we have two replicas are always running, and it will automatically replace any failed or terminated pods.

This approach provides high availability and scalability for stateless services without the need for persistent storage or stable network identities.

#### StatefulSets

StatefulSets are used for stateful applications that require persistent storage and stable network identities. They provide guarantees about the ordering and uniqueness of pod instances. In this project, we use StatefulSets for the following infrastructure components:

- **PostgreSQL**
- **RabbitMQ**

These components require persistent storage to maintain their state across pod restarts and rescheduling. StatefulSets ensure that each pod has a unique, stable network identity and persistent storage, which is crucial for databases and message brokers.

## Deployment

### Prerequisites

- Kubernetes cluster
- kubectl configured to interact with the cluster
- cert-manager installed in the cluster

### Steps

1. **Clone the repository**:
    ```sh
    git clone https://github.com/aadityamayankar/macra-k8
    cd macra-k8
    ```

2. **Deploy infrastructure components**:
    ```sh
    kubectl apply -f infrastructure/postgres/
    kubectl apply -f infrastructure/rabbitmq/
    kubectl apply -f infrastructure/redis/
    ```
    **Note:**
    * configure with the appropriate password_hash in infrastructure/rabbitmq/configmap.yaml
    * create a postgres-secret manifest for the postgresql deployment with the appropriate credentials and db name

3. **Deploy cert-manager components**:
    ```sh
    kubectl apply -f cert-manager/cluster-issuer.yaml
    kubectl apply -f cert-manager/certificate.yaml
    ```
    **Note:**
    * configure the cluster-issuer with the appropriate email address and private key secret name

4. **Deploy application components**:
    ```sh
    kubectl apply -f apps/authn/
    kubectl apply -f apps/authz/
    kubectl apply -f apps/eventsync/
    kubectl apply -f apps/opsadmin/
    kubectl apply -f apps/user/
    kubectl apply -f apps/utility-client-deployment.yaml
    ```
    **Note:**
    * create app-secret manifest(s) for the application deployment with the appropriate credentials 

5. **Deploy ingress**:
    ```sh
    kubectl apply -f ingress/ingress.yaml
    ```

## Notes

- Secrets are ignored by [.gitignore](http://_vscodecontentref_/33) as specified in the `*secret.yaml` rule.
- Ensure to replace hardcoded values in the manifests, such as email addresses and secret names, with appropriate values for your environment.

## TODO

- [ ] Helm charts for the applications and infrastructure components
- [ ] CI/CD pipeline for automated deployment
- [ ] Move secrets to a secret management service

## License

This project is licensed under the MIT License.