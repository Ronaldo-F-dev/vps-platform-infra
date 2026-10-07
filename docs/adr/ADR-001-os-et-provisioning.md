# ADR-001 - Choix de l'OS et stratégie de provisioning du VPS

- Statut : proposé
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
- À compléter : playbook rejoué sans changement, snapshot de départ.
