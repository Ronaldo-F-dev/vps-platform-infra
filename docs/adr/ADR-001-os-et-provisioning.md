# ADR-001 - Choix de l'OS et stratégie de provisioning du VPS

- Statut : accepté (2026-10-22)
- Date : 2026-10-07

## Contexte
VPS KVM unique, 6 vCPU, 11 Go RAM, disque de 96 Go (200 Go annoncés, écart à élucider).
Ubuntu 24.04.4 LTS déjà installé. Le serveur contenait des projets de test (K3s, Docker, nginx,
PostgreSQL) supprimés le 2026-10-07 : état de départ propre, RAM 0,8 Go utilisée.

## Décision
- OS : Ubuntu 24.04 LTS (26.04 non disponible chez l'hébergeur à ce jour).
- Provisioning : cloud-init pour l'amorce, Ansible (idempotent) pour le socle.
- OpenTofu : seulement si l'hébergeur expose un provider utile. Pas de Terraform décoratif.

## Alternatives écartées
- Configuration manuelle en SSH : non reproductible.
- Réinstaller l'OS : inutile, le serveur est sain après nettoyage.

## Conséquences
- Tout changement de l'hôte passe par un rôle Ansible versionné dans ce dépôt.
- Les écarts connus (disque, règle UFW 434, groupe docker) sont à traiter dans le Projet 1.

## Preuves
- Playbook `site.yml` : `ansible-playbook site.yml` rejoué, résultat attendu `changed=0` (voir `test-log.md`).
- Amorce cloud-init : `cloud-init/user-data.yml`.
- Pas de snapshot de départ : l'hébergeur n'en fournit pas (contrainte de la formation). Compensation : export de
  configuration hors VPS (ADR-010) et reconstruction documentée (`runbook-rebuild.md`).
- Disque : 96 Go constatés au lieu de 200 Go annoncés. Le budget de capacité du Projet 2 part de 96 Go.
