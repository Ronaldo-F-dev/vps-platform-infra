# Matrice des ports (état de départ : 2026-10-07, après nettoyage)

| Port | Protocole | Programme | Ouvert à | Raison | Statut |
|------|-----------|-----------|----------|--------|--------|
| 22   | TCP | sshd | Internet | Administration (clé SSH uniquement) | Voulu |
| 80   | TCP | (rien) | Internet (règle UFW) | Futur HTTP/ACME | A justifier |
| 443  | TCP | (rien) | Internet (règle UFW) | Futur HTTPS | A justifier |
| 434  | TCP | (rien) | Internet (règle UFW) | Aucune (faute de frappe probable de 443) | A supprimer |
