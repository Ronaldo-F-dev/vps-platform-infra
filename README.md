# vps-platform-infra

Socle du VPS (Ubuntu 24.04 LTS) : accès, sécurité, automatisation Ansible, sauvegardes.
Fait partie du parcours DevOps Senior (Projet 1). K3s n'est installé qu'après validation de ce socle.

## Structure
- `ansible/` : inventaire, playbooks, rôles
- `docs/adr/` : décisions d'architecture
- `docs/ports-matrix.md` : qui a le droit d'entrer par quel port

## Règles
- Aucun secret dans ce dépôt.
- Avant de modifier SSH ou le firewall : garder une seconde session SSH ouverte.
