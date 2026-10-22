# ADR-010 - Sauvegarde de l'hôte et services permanents

- Statut : accepté
- Date : 2026-10-22

## Contexte
Pas de snapshot hébergeur disponible. Le dépôt Git contient déjà toute la configuration de l'hôte, sauf
les éléments propres à la machine (clés d'hôte SSH, état d'UFW réel, `authorized_keys` effectif).

## Décision
- Snapshot ≠ backup : un snapshot permet un retour rapide mais disparaît avec le VPS ; un backup protège de
  la perte du serveur. Ici seul le backup est possible.
- Niveau 1 : la configuration est dans Git (`vps-platform-infra`), sur GitHub.
- Niveau 2 : `playbooks/backup-config.yml` copie hors VPS la configuration effective vers `backups/<date>/`
  (ignoré par Git). Fréquence : avant tout changement majeur (SSH, firewall, K3s).
- Niveau 3 : copier ce dossier hors du Mac (autre support). La responsabilité revient à l'administrateur.
- Aucune donnée applicative n'existe encore ; la sauvegarde des données et son test de restauration
  relèvent du Projet 6 (ADR-007).

## Services permanents justifiés
| Service | Raison |
|---|---|
| sshd | Administration |
| fail2ban | Limiter la force brute sur SSH |
| unattended-upgrades | Correctifs de sécurité automatiques, sans reboot |
| ufw | Pare-feu |
| host-healthcheck.timer | Seuils disque/mémoire/reboot/unités en échec, écrits dans le journal |
| systemd-journald (persistant, plafonné 500 Mo / 1 mois) | Journaux conservés après reboot |

Le monitoring hôte est volontairement léger (pas d'agent) : Prometheus et node_exporter arrivent au Projet 5.

## Conséquences
- Reconstruction possible depuis Git + cloud-init + clés publiques (`runbook-rebuild.md`).
- Non couvert : la clé d'hôte SSH est régénérée sur un serveur neuf (avertissement `known_hosts` normal).
