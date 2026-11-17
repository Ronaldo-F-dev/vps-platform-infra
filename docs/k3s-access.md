# Accéder à l'API Kubernetes

Le port 6443 n'est pas ouvert sur Internet (ADR-002, ADR-009). Deux accès possibles.

## Sur l'hôte
```bash
sudo k3s kubectl get nodes
```
