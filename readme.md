# Kubernetes Deployment for Macra

This repository contains Kubernetes manifests for deploying various services and infrastructure components for the Macra application.

## Project Structure

## Components

### Applications

- **authn**: Authentication service
- **authz**: Authorization service
- **eventsync**: Event synchronization service
- **opsadmin**: Operational admin service
- **user**: User service
- **utility-client**: Utility client for deployment

### Infrastructure

- **PostgreSQL**: Database service
- **RabbitMQ**: Message broker service
- **Redis**: In-memory data structure store

### Cert-Manager

- **ClusterIssuer**: LetsEncrypt ClusterIssuer configuration
- **Certificate**: TLS certificate for `api.macra.live`

### Ingress

- **Ingress**: NGINX Ingress configuration for routing traffic to services

## Deployment

### Prerequisites

- Kubernetes cluster
- kubectl configured to interact with the cluster
- cert-manager installed in the cluster

### Steps

1. **Clone the repository**:
    ```sh
    git clone <repository-url>
    cd <repository-directory>
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