---
name: jurisprudence
description: "Rechercher de la jurisprudence française : Cour de cassation, Conseil d'État, Conseil constitutionnel. Par thème, numéro de pourvoi, ECLI, ou domaine juridique sur une période."
---

# /jurisprudence — Recherche de jurisprudence

Recherche de décisions de justice françaises via le MCP Légifrance.

## Workflow

### Par thème / mots-clés
1. Déterminer le fonds : `JURI` (judiciaire), `CETAT` (administratif), `CONSTIT` (constitutionnel)
2. Lancer la recherche :
```
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "DATE_DESC", typePagination: "DEFAUT"
})
```
3. `get_jurisprudence(textId: "JURITEXT...")` pour le texte complet
4. Optionnel : `get_jurisprudence_classification(textId: "JURITEXT...")` pour le plan de classement

### Par numéro de pourvoi
```
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "NUM_AFFAIRE", criteres: [{
    typeRecherche: "EXPRESSION_EXACTE", valeur: "21-12.345", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 5,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

### Par ECLI
```
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ECLI", criteres: [{
    typeRecherche: "EXPRESSION_EXACTE", valeur: "ECLI:FR:CCASS:2021:...", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 5,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

### Approche graph-first (sujet conceptuel)
```
query_legal_graph(concept: "<concept juridique>")
```
Si le graphe retourne des décisions → accès direct via `get_jurisprudence`.

## Filtres utiles

| Filtre | Facette | Valeurs |
|--------|---------|---------|
| Juridiction | `JURIDICTION_JUDICIAIRE` | `COUR_CASSATION`, `COURS_APPEL` |
| Formation Cass. | `CASSATION_FORMATION` | `CHAMBRE_CIVILE_1`, `CHAMBRE_CIVILE_2`, `CHAMBRE_CIVILE_3`, `CHAMBRE_COMMERCIALE`, `CHAMBRE_SOCIALE`, `CHAMBRE_CRIMINELLE` |
| Bulletin | `CASSATION_TYPE_PUBLICATION_BULLETIN` | `T` pour arrêts publiés |

## Fonds par juridiction

- **JURI** : Cour de cassation, cours d'appel, tribunaux judiciaires
- **CETAT** : Conseil d'État, cours administratives d'appel, tribunaux administratifs
- **CONSTIT** : Conseil constitutionnel (QPC, DC)
- **JUFI** : Juridictions de première instance

## Réponse attendue

- Citation complète : `Cass. [formation], [date], n° [pourvoi], [publication]`
- Solution (cassation, rejet, annulation...)
- Résumé et motifs principaux
- ECLI
- Articles visés
- Identifiant Légifrance (JURITEXT...)
