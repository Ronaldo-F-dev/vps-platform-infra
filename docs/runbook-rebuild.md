# Runbook : reconstruction, patch et rollback de l'hôte

## 1. Reconstruire le socle sur un VPS neuf
1. Fournir `cloud-init/user-data.yml` à la création (ou créer `ronaldo` et ses clés à la main).
2. Définir le mot de passe sudo de `ronaldo` (`sudo passwd ronaldo` depuis un accès initial). Jamais dans Git.
3. Mettre à jour `ansible_host` dans `ansible/inventory/hosts.yml`. Accepter la nouvelle clé d'hôte
   (`ssh-keygen -R <ip>` puis première connexion).
4. `cd ansible && ansible-playbook playbooks/ping.yml` : l'accès fonctionne.
5. Garder UNE SECONDE SESSION SSH OUVERTE, puis `ansible-playbook site.yml -K`.
6. Relancer : le résultat doit être `changed=0` (idempotence).
7. `ansible-playbook playbooks/verify.yml -K` et consigner le résultat dans `docs/test-log.md`.
8. Ordre pour la suite (Projets 2+) : hôte -> K3s -> Argo CD / GitOps -> données restaurées -> DNS.

## 2. Procédure de patch
- Automatique : `unattended-upgrades` applique les correctifs de sécurité, sans reboot.
- Manuel : `ansible-playbook playbooks/patch.yml -K` (ne redémarre jamais).
- Si « REBOOT EN ATTENTE » : planifier un reboot manuel, avec la seconde session SSH et la clé `breakglass`
  prêtes. Après reboot : `ssh`, `systemctl --failed`, `ufw status`, `host-healthcheck`.
- Avant un patch majeur : `ansible-playbook playbooks/backup-config.yml -K` (ADR-010).

## 3. Rollback d'un changement de configuration
Tout changement passe par Git, donc :
1. `git revert <commit>` du changement fautif (ne jamais réécrire l'historique).
2. `ansible-playbook site.yml -K` pour réappliquer l'état précédent.
3. Si SSH est cassé : utiliser la session déjà ouverte, sinon la clé `breakglass`.
   Un fichier sshd invalide n'est jamais installé (`validate: sshd -t`).
4. Un fichier supprimé de Git n'est pas supprimé du serveur : le retirer explicitement avec une tâche
   `state: absent`, puis relancer.

## 4. Si l'accès SSH est perdu
Il n'y a ni console ni snapshot. Dans l'ordre : autre clé (`breakglass`), autre réseau (KI-001),
puis contacter la formation. Ne jamais tenter de « corriger » SSH sans seconde session ouverte.
