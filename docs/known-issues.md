# Problèmes connus

## KI-001 - Coupures SSH intermittentes (ouvert)

**Symptôme.** `ansible-playbook` s'arrête parfois avec `UNREACHABLE: Failed to connect to the host via ssh`
(3 fois sur environ 8 lancements le 2026-10-07 et 2026-10-09). Des `ssh` manuels rapprochés
sont aussi réinitialisés (`Connection reset by peer`).

**Faits établis.**
- `fail2ban` n'a pas banni l'IP de l'administrateur.
- `auth.log` ne contient aucune ligne `drop connection` / `MaxStartups`.
- Les connexions qui échouent n'apparaissent pas dans le log de `sshd` : elles sont coupées avant lui.
- Une rafale de 12 connexions a donné 2 échecs (2026-10-09 09:13) ; quatre rafales identiques
  quelques minutes plus tard : 0 échec sur 48. Les échecs viennent par épisodes.
- L'adresse publique de l'administrateur change (143.105.211.245, 143.105.211.240, 102.38.155.200).

**Non établi.** Quel équipement coupe la connexion (réseau de l'administrateur, hébergeur, serveur).

**Mitigation en place.** `retries = 3` et `pipelining = True` dans `ansible/ansible.cfg`.
Le playbook étant idempotent, relancer après un échec est sans danger.

**Pour avancer si le problème revient.** Noter l'heure, refaire une rafale depuis un autre réseau,
puis capturer le trafic du port 22 côté serveur pendant l'échec (`tcpdump`).
