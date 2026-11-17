# Stockage et datastore K3s

## Datastore
SQLite embarqué (ADR-002) : `/var/lib/rancher/k3s/server/db/`. Un seul noeud, pas d'etcd. La commande
`k3s etcd-snapshot` n'existe pas avec SQLite.

## Volumes persistants
- StorageClass `local-path` (par défaut) : les volumes sont des dossiers sous
  `/var/lib/rancher/k3s/storage/` sur le disque du VPS.
- Pas de réplication, pas de chiffrement, pas de limite de taille réellement appliquée par le disque :
  la taille demandée n'est qu'une déclaration, bornée côté Kubernetes par `requests.storage` dans le quota.
- Politique de récupération `Delete` : supprimer un PVC supprime les données.
- Un pod lié à un volume local reste sur ce noeud (acceptable : un seul noeud).

## Sauvegarde supportée
- Datastore et clés : `ansible-playbook playbooks/k3s-backup.yml -K` (copie à froid, K3s arrêté quelques
  secondes). L'archive contient les secrets : à chiffrer avant tout stockage distant.
- Données des volumes : copie du dossier `/var/lib/rancher/k3s/storage/` ; l'outil et le test de
  restauration sont décidés au Projet 6 (ADR-007). Tant que ce n'est pas fait, les volumes ne sont PAS sauvegardés.
- Manifests : déjà dans le dépôt GitOps.

## Restauration (principe, à chronométrer au Projet 6)
1. `systemctl stop k3s`
2. Restaurer `server/db`, `server/token`, `server/tls`, `server/cred` et `config.yaml` depuis l'archive.
3. `systemctl start k3s`, puis `playbooks/k3s-verify.yml`.
