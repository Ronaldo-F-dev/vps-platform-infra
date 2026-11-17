# Accéder à l'API Kubernetes

Le port 6443 n'est pas ouvert sur Internet (ADR-002, ADR-009). Deux accès possibles.

## Sur l'hôte
```bash
sudo k3s kubectl get nodes
```

## Depuis le poste d'administration (tunnel SSH)
```bash
# 1. Récupérer le kubeconfig (identifiants cluster-admin : ne jamais le commiter)
cd ansible
ansible vps -b -K -m ansible.builtin.fetch \
  -a "src=/etc/rancher/k3s/k3s.yaml dest=../backups/kubeconfig/k3s.yaml flat=true"

# 2. Ouvrir le tunnel (le kubeconfig vise déjà https://127.0.0.1:6443)
ssh -N -L 6443:127.0.0.1:6443 ronaldo@<ip-du-vps>

# 3. Dans un autre terminal
export KUBECONFIG=$PWD/../backups/kubeconfig/k3s.yaml
kubectl get nodes
```
Le dossier `backups/` est ignoré par Git. Supprimer le kubeconfig du poste en cas de perte ou de revente.
