# ADR-009 - Firewall et politique de ports

- Statut : accepté
- Date : 2026-10-22

## Décision
- UFW, entrée refusée par défaut, sortie autorisée, géré uniquement par le rôle `firewall`.
- Ports ouverts : 22 (SSH), 80 et 443 (ingress K3s, ACME), cf. `ports-matrix.md`.
- Réseaux internes K3s (10.42.0.0/16 pods, 10.43.0.0/16 services) autorisés en entrée : sans eux UFW coupe
  le trafic pod -> API. Le port 6443 n'est volontairement pas ouvert (accès par tunnel SSH).
- Les règles `allow` sont posées AVANT l'activation d'UFW pour ne jamais se couper de SSH.
- Règles héritées supprimées : 434/tcp (faute de frappe) et profil `OpenSSH` (doublon).

## Alternatives écartées
- nftables/iptables à la main : moins lisible, pas d'avantage ici.
- Fermer 80/443 jusqu'au Projet 2 : plus strict, mais rouvre une étape manuelle risquée. Ouverts d'avance
  et signalés « à refermer si inutilisé ». Aucun programme n'écoute dessus pour l'instant.

## Conséquences
- Attention : Docker publie ses ports en contournant UFW. Tout conteneur avec `-p` est exposé même si le
  port n'est pas autorisé. À traiter avant d'utiliser Docker hors k3d.
- K3s ajoute ses propres règles iptables (DNAT pour les ports publiés par servicelb et les NodePorts) qui
  peuvent contourner UFW comme Docker. À vérifier après installation par un scan de ports EXTERNE
  (`nmap -p 1-65535 <ip>` depuis une autre machine) : seuls 22, 80 et 443 doivent répondre.
  Résultat à consigner dans `ports-matrix.md` ; aucun NodePort ne doit être créé sans mise à jour de ce document.
