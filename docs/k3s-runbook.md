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
