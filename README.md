# vps-platform-infra

Socle du VPS (Ubuntu 24.04 LTS) : accès, sécurité, automatisation Ansible, sauvegardes.
Fait partie du parcours DevOps Senior (Projet 1). K3s n'est installé qu'après validation de ce socle.

## Structure
- `ansible/` : inventaire, playbooks, rôles
- `docs/adr/` : décisions d'architecture
- `cloud-init/` : amorce d'un VPS neuf
- `docs/ports-matrix.md` : qui a le droit d'entrer par quel port
- `docs/admin-access.md` : compte admin, clés, accès de secours
- `docs/runbook-rebuild.md` : reconstruction, patch, rollback
- `docs/test-log.md` : preuves et tests du Projet 1
- `docs/known-issues.md` : problèmes connus (KI-001)

## Rôles Ansible
`firewall`, `ssh_hardening`, `accounts`, `updates`, `fail2ban`, `host_monitoring` (tous dans `ansible/site.yml`).
Playbooks utiles : `verify.yml` (contrôles lecture seule), `patch.yml`, `backup-config.yml`.

## Règles
- Aucun secret dans ce dépôt.
- Avant de modifier SSH ou le firewall : garder une seconde session SSH ouverte.
