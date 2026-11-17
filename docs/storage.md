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
