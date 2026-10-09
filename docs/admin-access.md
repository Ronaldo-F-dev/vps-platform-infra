# Accès d'administration

Dernière vérification : 2026-10-09. Source de vérité des clés : `ansible/roles/accounts/defaults/main.yml`.

## Compte
- Compte d'administration : `ronaldo` (non-root). `root` ne peut pas se connecter en SSH.
- Authentification SSH : clé uniquement (mot de passe refusé).
- `sudo` : mot de passe demandé (vérifié). Le mot de passe n'est stocké nulle part dans le dépôt.
- Groupe `docker` : `ronaldo` en est membre. Ce groupe équivaut à un accès administrateur sans mot de passe
  (un conteneur peut monter tout le disque). Conservé car Docker servira aux labs k3d.

## Clés autorisées pour `ronaldo`

| Nom dans Git | Empreinte | Connexions vues dans auth.log | Statut |
|---|---|---|---|
| main | SHA256:8G8Wg9+oAdUPzjae5o4XV0WYdW44NLlYpaR2nPOlP8w | 132 | Clé principale (Mac de l'administrateur) |
| breakglass | SHA256:037Lvb+fAvYbzjfPyfQRqlIz/ks+wvFSYLZeQCAh8kE | 2 (tests) | Secours, privée à conserver hors du Mac |
| other-computer-kSsgWg | SHA256:kSsgWgcQwdDQwVI5QxcRBuLw9S/+RO4ZPSC5wL60/T8 | 127 | Autre poste (réseau de la formation) |
| other-computer-1KCvW5 | SHA256:1KCvW/54kuqvN3sG5YfoxwbocopvVPfY9RCVQz29bQ4 | 15 | Autre poste (réseau de la formation) |

### Clés présentes sur le serveur mais non gérées par Git (décision : conservées)
| Commentaire | Empreinte | Connexions vues |
|---|---|---|
| github | SHA256:c2CmNOMCnhV4iDdn0MtIsyRmCY4q4D3p3k21M93fHfk | 0 |
| symfony | SHA256:hhAKlaCWjtfS6E7JjIUhFKk+8vzPCFUobbkerG76cgo | 0 |

Jamais vues dans les logs conservés (quelques semaines seulement). Retrait reporté à la demande de
l'administrateur (2026-10-09). Le rôle `accounts` utilise `exclusive: false` : il n'enlève aucune clé.
Risque assumé : deux accès non identifiés restent valides.

## Autre compte
- `ubuntu` (uid 1000, groupe `sudo`) : compte par défaut de l'image, jamais utilisé depuis le 2026-07-13.
  Clés et droits `sudo` non lisibles par `ronaldo`. A clarifier avec la formation avant toute action.

## Accès de secours
- Il n'existe ni console hébergeur ni snapshot (contrainte imposée par la formation).
- Le seul secours est la clé `breakglass` (privée hors du Mac). Sa perte combinée à celle de la clé
  principale rend le serveur inaccessible.
