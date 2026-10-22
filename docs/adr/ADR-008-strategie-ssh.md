# ADR-008 - Stratégie SSH

- Statut : accepté
- Date : 2026-10-22

## Contexte
Seul moyen d'accès au serveur : pas de console hébergeur ni de snapshot. Une erreur SSH peut rendre le serveur
définitivement inaccessible.

## Décision
- Compte d'administration non-root `ronaldo` ; `root` interdit en SSH.
- Authentification par clé uniquement (mot de passe et clavier-interactif refusés), `MaxAuthTries 3`,
  `LoginGraceTime 30`, `X11Forwarding no` (rôle `ssh_hardening`, drop-in `10-hardening.conf`).
- La configuration est écrite avec `validate: sshd -t` : un fichier invalide n'est jamais installé.
- Accès de secours : clé `breakglass`, dont la privée est conservée hors du Mac (`docs/admin-access.md`).
- `fail2ban` (jail sshd, 5 échecs / 10 min -> ban 1 h) contre le bruit de force brute. Aucune IP d'admin
  en liste blanche, car elle change (KI-001).
- Port SSH conservé à 22 : le changer n'apporte pas de sécurité réelle et complique l'accès de secours.

## Alternatives écartées
- `exclusive: true` sur les clés autorisées : supprimerait les clés `github` et `symfony` de propriétaire
  inconnu, ce qui pourrait couper l'accès de quelqu'un de légitime. Reporté tant que la formation n'a pas répondu.
- Restriction du port 22 à une IP : l'IP de l'admin change.

## Conséquences
- Règle : toute modification SSH se teste depuis une seconde session ouverte avant de fermer la première.
- Risque assumé : deux clés non identifiées restent valides (documenté).
- Perte simultanée de `main` et `breakglass` = serveur inaccessible.
