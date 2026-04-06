---
name: dossier
description: "Consulter le parcours législatif d'une loi : dossier législatif, travaux préparatoires, débats parlementaires. Utile pour comprendre l'intention du législateur."
---

# /dossier — Dossier législatif et travaux préparatoires

Parcours complet d'une loi : de la proposition/projet à la promulgation.

## Workflow

### Trouver le dossier
1. Si on a le numéro de la loi → `search_texts(fond: "LODA_DATE", typeChamp: "NUM", ...)` d'abord
2. Lister les dossiers : `list_texts(category: "dossiers_legislatifs", extra_params: {"legislatureId": 17, "type": "LOI_PUBLIEE"}, pageSize: 20)`
3. Types disponibles : `LOI_PUBLIEE`, `ORDONNANCE_PUBLIEE`, `PROJET_LOI`, `PROPOSITION_LOI`

### Consulter le dossier
```
get_dossier_legislatif(id: "JORFDOLE...")
```

### Débats parlementaires
```
get_debate(id: "<ID du débat>")
```

## Réponse attendue

- Titre du projet/proposition de loi
- Dates clés : dépôt, lectures, commissions, vote, promulgation
- Rapporteurs et commissions saisies
- Résumé des débats et amendements significatifs
- Lien vers la loi promulguée
