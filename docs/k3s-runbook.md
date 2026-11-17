# Runbook K3s

## 1. Installation
Prérequis : `site.yml` appliqué et `playbooks/verify.yml` sans erreur, `playbooks/backup-config.yml` fait,
une seconde session SSH ouverte.

1. `cd ansible && ansible-playbook platform.yml -K`
2. Relancer : le second passage ne doit rien changer (`changed=0`).
3. `ansible-playbook playbooks/k3s-verify.yml -K`, puis inscrire la version exacte dans ADR-002.
4. Configurer l'accès à l'API (`docs/k3s-access.md`), puis suivre `docs/bootstrap.md` du dépôt GitOps.
5. Scanner les ports depuis une autre machine (`nmap -p 1-65535 <ip>`) et consigner le résultat dans
   `docs/ports-matrix.md` (ADR-009).

## 2. Mise à niveau
1. Lire les notes de version et vérifier la compatibilité des addons (cert-manager, Traefik).
2. Tester d'abord sur un clone k3d (lab « Upgrade »).
3. Sauvegarder : `ansible-playbook playbooks/k3s-backup.yml -K`.
4. Changer `k3s_version` (une version mineure à la fois, jamais de saut), commiter, puis
   `ansible-playbook platform.yml -K`.
5. `ansible-playbook playbooks/k3s-verify.yml -K`.
6. Rollback : K3s ne gère pas le retour arrière du datastore. Restaurer l'archive de l'étape 3
   (voir `docs/storage.md`) et réinstaller la version précédente avec le rôle.
