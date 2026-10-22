# ADR-002 - K3s mono-noeud : limites, datastore et stratégie de sauvegarde

- Statut : proposé (à confirmer au démarrage du Projet 2)
- Date : 2026-10-22

## Contexte
Un seul VPS (6 vCPU, ~11 Go de RAM, 96 Go de disque, sans console ni snapshot hébergeur). K3s sera installé
après validation du socle hôte (Projet 1).

## Décision
- K3s natif, un seul noeud (serveur + agent). La version exacte sera relevée et inscrite ici à l'installation.
- Datastore : SQLite embarqué (défaut K3s mono-noeud). Pas d'etcd ni de base externe : inutile sans HA.
- Stockage : `local-path` (volumes sur le disque du VPS). Pas de stockage distribué.
- Addons limités au nécessaire ; chaque composant permanent doit avoir un propriétaire, une limite de
  ressources et une procédure de retrait.
- Sauvegarde : snapshots du datastore K3s (`k3s etcd-snapshot` n'est pas disponible avec SQLite : copie de
  `/var/lib/rancher/k3s/server/db/` à froid ou via l'outil retenu en Projet 6) + export hors VPS (ADR-007 à venir).

## Alternatives écartées
- Multi-noeuds simulés (k3d) présentés comme de la HA : trompeur. k3d reste réservé aux labs.
- Longhorn / Ceph : trop lourds pour 11 Go de RAM et sans bénéfice sur un noeud unique.

## Conséquences
- Une panne du VPS est une panne totale. On valide la reconstruction et la restauration, pas la continuité.
- Les namespaces dev/staging/prod séparent droits et ressources, pas l'infrastructure.
- Le stockage local et le datastore sont des dépendances fortes : la sauvegarde hors VPS est obligatoire.

## Preuves
- À compléter en Projet 2 : version K3s, test de restauration chronométré.
