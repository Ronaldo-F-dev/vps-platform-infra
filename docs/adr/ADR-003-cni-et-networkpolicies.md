# ADR-003 - CNI et enforcement effectif des NetworkPolicies

- Statut : proposé (à confirmer par le test `scripts/test-networkpolicy.sh` du dépôt GitOps)
- Date : 2026-11-15

## Contexte
Le document maître interdit de présenter des NetworkPolicies comme effectives sans CNI capable de les
faire respecter. Hôte : 6 vCPU, ~11 Go de RAM, budget « K3s et composants système » de 1 à 1,5 Go.
