# ADR-009 - Firewall et politique de ports

- Statut : accepté
- Date : 2026-10-22

## Décision
- UFW, entrée refusée par défaut, sortie autorisée, géré uniquement par le rôle `firewall`.
- Ports ouverts : 22 (SSH), 80 et 443 (ingress K3s, ACME), cf. `ports-matrix.md`.
- Les règles `allow` sont posées AVANT l'activation d'UFW pour ne jamais se couper de SSH.
- Règles héritées supprimées : 434/tcp (faute de frappe) et profil `OpenSSH` (doublon).

## Alternatives écartées
- nftables/iptables à la main : moins lisible, pas d'avantage ici.
- Fermer 80/443 jusqu'au Projet 2 : plus strict, mais rouvre une étape manuelle risquée. Ouverts d'avance
  et signalés « à refermer si inutilisé ». Aucun programme n'écoute dessus pour l'instant.

## Conséquences
- Attention : Docker publie ses ports en contournant UFW. Tout conteneur avec `-p` est exposé même si le
  port n'est pas autorisé. À traiter avant d'utiliser Docker hors k3d.
- K3s ajoutera ses propres règles réseau : vérifier la matrice des ports après son installation.
