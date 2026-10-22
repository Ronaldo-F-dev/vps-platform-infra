# Journal des tests du Projet 1

Règle : on ne coche que ce qui a été exécuté. Les lignes « À exécuter » le seront avec
`ansible-playbook playbooks/verify.yml -K` quand le serveur est joignable.

| Test | Résultat attendu | Statut | Date / preuve |
|---|---|---|---|
| SSH : root refusé, mot de passe refusé | `permitrootlogin no`, `passwordauthentication no` | Constaté lors de l'application du rôle (historique Git, `admin-access.md`) ; à reconfirmer par `verify.yml` | 2026-10-09 |
| sudo exige un mot de passe | invite de mot de passe | Vérifié (`admin-access.md`) | 2026-10-09 |
| UFW actif, entrée refusée par défaut, 22/80/443 | `ufw status numbered` | Vérifié (`ports-matrix.md`) | 2026-10-07 |
| Playbook idempotent (`idempotence-demo.yml`) | 2e lancement `changed=0` | Playbook présent ; sortie `changed=0` non archivée, à relancer | À exécuter |
| Mises à jour auto activées, sans reboot automatique | `Automatic-Reboot "false"` | Rôle appliqué ; à reconfirmer par `verify.yml` | À exécuter |
| fail2ban actif (jail sshd) | `fail2ban-client status sshd` | Rôle écrit, **pas encore appliqué** | À exécuter |
| Journaux persistants + healthcheck | timer actif, `journalctl -t host-healthcheck` | Rôle écrit, **pas encore appliqué** | À exécuter |
| `site.yml` rejoué avec les nouveaux rôles | 1er lancement : changes ; 2e : `changed=0` | **Pas encore appliqué** (VPS injoignable le 2026-10-22 : timeout port 22, cf. KI-001) | À exécuter |
| Syntaxe de `site.yml`, `verify.yml`, `backup-config.yml` | `--syntax-check` OK | OK | 2026-10-22 |
| Test de reconstruction sur un VPS neuf | socle reproduit | Non réalisé (pas de second serveur) | Écart assumé |
| Snapshot de départ | image restaurable | Impossible (contrainte formation) | Écart assumé, voir ADR-010 |
