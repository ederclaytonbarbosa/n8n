# n8n on Kubernetes

Reference implementation for running n8n with PostgreSQL on Kubernetes. The manifests include persistent storage, an NGINX Ingress, Kubernetes Secrets, and a PostgreSQL backend.

> This repository contains example values only. Never commit production credentials, kubeconfig files, tokens, or private domains.

## Architecture

- n8n application exposed through an NGINX Ingress
- PostgreSQL database with persistent storage
- Longhorn-backed persistent volume claims
- Credentials provided through a Kubernetes Secret
- HTTPS endpoint using the fictitious domain `n8n.example.com`

## Prerequisites

- A running Kubernetes cluster
- `kubectl` configured for the target cluster
- NGINX Ingress Controller
- Longhorn, or another StorageClass after updating the PVC manifests
- TLS/certificate management configured for your environment

## Deploy

1. Create a local secret manifest from the safe example:

   ```bash
   cp n8n-secret.example.yaml n8n-secret.yaml
   ```

2. Replace every `CHANGE_ME_*` value in `n8n-secret.yaml`. This file is ignored by Git.

3. Replace `n8n.example.com` in the application and Ingress manifests with your own domain.

4. Apply the manifests:

   ```bash
   kubectl apply -f namespace.yaml
   kubectl apply -f n8n-secret.yaml
   kubectl apply -f n8n-pvc-postgres.yaml
   kubectl apply -f n8n-pvc.yaml
   kubectl apply -f n8n-service-postgres.yaml
   kubectl apply -f n8n-service.yaml
   kubectl apply -f n8n-deployment-postgres.yaml
   kubectl apply -f n8n-deployment.yaml
   kubectl apply -f n8n-ingress.yaml
   ```

5. Check the rollout:

   ```bash
   kubectl -n n8n get pods,svc,ingress,pvc
   kubectl -n n8n rollout status deployment/postgres
   kubectl -n n8n rollout status deployment/n8n
   ```

## Security notes

- Use an external secret manager in production, such as External Secrets Operator, Vault, or a cloud-native secrets service.
- Pin and regularly update container image versions for production workloads.
- Configure TLS, NetworkPolicies, resource requests/limits, backups, and monitoring before production use.
- Rotate any credential that has previously been committed to Git.

## Portfolio context

This project demonstrates Kubernetes workload deployment, persistent storage, Ingress configuration, PostgreSQL integration, and secure configuration patterns for an n8n automation platform.
