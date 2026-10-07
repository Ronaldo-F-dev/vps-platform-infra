# Matrice des ports

Dernière vérification : 2026-10-07, après application du rôle `firewall` (`ufw status numbered`).
Politique par défaut : entrée refusée, sortie autorisée. Les règles sont gérées par Ansible
(`ansible/roles/firewall/defaults/main.yml`) : ne rien ajouter à la main.

| Port | Protocole | Programme | Ouvert à | Raison | Statut |
|------|-----------|-----------|----------|--------|--------|
| 22   | TCP | sshd | Internet (IPv4 + IPv6) | Administration, clé SSH uniquement, root interdit | Voulu |
| 80   | TCP | (aucun pour l'instant) | Internet (IPv4 + IPv6) | Réservé à l'ingress et à ACME (Projet 2) | Ouvert d'avance, à refermer si inutilisé |
| 443  | TCP | (aucun pour l'instant) | Internet (IPv4 + IPv6) | Réservé à l'ingress HTTPS (Projet 2) | Ouvert d'avance, à refermer si inutilisé |

## Historique
- 434/tcp : supprimé (aucune raison d'être, faute de frappe probable de 443).
- Profil `OpenSSH` : supprimé (doublon de 22/tcp).
