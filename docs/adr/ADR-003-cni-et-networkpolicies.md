# ADR-003 - CNI et enforcement effectif des NetworkPolicies

- Statut : proposé (à confirmer par le test `scripts/test-networkpolicy.sh` du dépôt GitOps)
- Date : 2026-11-15

## Contexte
Le document maître interdit de présenter des NetworkPolicies comme effectives sans CNI capable de les
faire respecter. Hôte : 6 vCPU, ~11 Go de RAM, budget « K3s et composants système » de 1 à 1,5 Go.

## Décision
- Conserver le CNI livré avec K3s : Flannel (VXLAN) pour le réseau de pods.
- K3s embarque en plus le contrôleur de NetworkPolicies de kube-router, activé par défaut tant que
  `disable-network-policy` n'est pas posé. C'est lui qui fait respecter les policies ; Flannel seul ne
  sait pas le faire.
- Aucune option de désactivation n'est ajoutée dans la configuration K3s (voir rôle `k3s`).
- L'enforcement n'est considéré comme prouvé qu'après le test de flux bloqué puis autorisé.
