# Stockage et datastore K3s

## Datastore
SQLite embarqué (ADR-002) : `/var/lib/rancher/k3s/server/db/`. Un seul noeud, pas d'etcd. La commande
`k3s etcd-snapshot` n'existe pas avec SQLite.
