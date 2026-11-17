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

## 3. Retrait d'un addon
- Composant packagé (Traefik, servicelb, metrics-server, local-storage) : l'ajouter à `k3s_disable`,
  appliquer `platform.yml`, puis SUPPRIMER à la main les ressources déjà déployées (K3s ne les retire pas
  après coup), par exemple `kubectl -n kube-system delete helmchart traefik traefik-crd`.
- Composant installé par le dépôt GitOps (cert-manager) : `kubectl delete -f` du manifest concerné, puis
  retirer le fichier du dépôt. Les CRD sont conservées (`keep: true`) : les supprimer explicitement si
  le retrait est définitif, après avoir vérifié qu'aucun `Certificate` n'existe.
- Mettre à jour `docs/ports-matrix.md` et l'ADR concerné.

## 4. Vérification de santé
```bash
ansible-playbook playbooks/k3s-verify.yml -K     # preuves et assertions
sudo k3s kubectl get nodes,pods -A               # état général
sudo k3s kubectl top nodes                       # consommation (metrics-server)
sudo journalctl -u k3s --since "1 hour ago"      # journal du service
df -h /var/lib/rancher                           # disque : alerte avant saturation
sudo k3s kubectl get certificate -A              # certificats prêts et échéances
```
Conserver 20 à 25 % de RAM libre (document maître) : `free -h` et `host-healthcheck`.
