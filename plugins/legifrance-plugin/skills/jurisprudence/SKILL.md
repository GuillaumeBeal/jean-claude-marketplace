---
name: jurisprudence
description: "Rechercher de la jurisprudence francaise : Cour de cassation, Conseil d'Etat, Conseil constitutionnel. Par theme, numero de pourvoi, ECLI, ou domaine juridique sur une periode."
---

# /jurisprudence — Recherche de jurisprudence

Recherche de decisions de justice francaises via le MCP Legifrance.

## Workflow

### Par theme / mots-cles
1. Determiner le fonds : `JURI` (judiciaire) ou `CETAT` (administratif)
2. Lancer la recherche :
```
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-cles>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "<debut>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```
3. `displayJuriUsingPOST(textId: "JURITEXT...")` pour le texte complet

### Par numero de pourvoi
```
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "NUM_AFFAIRE", criteres: [{
    typeRecherche: "EXACTE", valeur: "21-12.345", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 5,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

### Par ECLI
```
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "ECLI", criteres: [{
    typeRecherche: "EXACTE", valeur: "ECLI:FR:CCASS:2021:...", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 5,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

## Filtres utiles

| Filtre | Valeurs |
|--------|---------|
| Juridiction | `COUR_CASSATION`, `TRIBUNAL_ADMINISTATIF`, `COURS_APPEL` |
| Formation Cass. | `CHAMBRE_CIVILE_1`, `CHAMBRE_CIVILE_2`, `CHAMBRE_CIVILE_3`, `CHAMBRE_COMMERCIALE`, `CHAMBRE_SOCIALE`, `CHAMBRE_CRIMINELLE` |
| Bulletin | `"T"` pour arrets publies au Bulletin |

## Fonds par juridiction

- **JURI** : Cour de cassation, cours d'appel, tribunaux judiciaires
- **CETAT** : Conseil d'Etat, cours administratives d'appel, tribunaux administratifs
- **CONSTIT** : Conseil constitutionnel (QPC, DC)

## Reponse attendue

- Juridiction, formation, date
- Numero de pourvoi / d'affaire
- Solution (cassation, rejet, annulation...)
- Resume et motifs principaux
- ECLI
- Articles vises
