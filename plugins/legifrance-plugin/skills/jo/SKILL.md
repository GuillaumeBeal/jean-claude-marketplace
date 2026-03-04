---
name: jo
description: "Consulter les derniers textes publies au Journal Officiel de la Republique Francaise. Derniers JO, recherche par date ou par contenu."
---

# /jo — Journal Officiel

Acces aux publications du Journal Officiel via le MCP Legifrance.

## Workflow

### Derniers JO publies
```
getLastNJoUsingPOST(nbElement: 5)
```
Ajuster `nbElement` selon la demande (par defaut 5, jusqu'a 30).

### Explorer un JO specifique
1. `getLastNJoUsingPOST` ou recherche par date pour obtenir l'identifiant
2. `displayJorfContUsingPOST(id: "JORFCONT...")` → sommaire du JO
3. `displayJorfUsingPOST(textCid: "JORFTEXT...")` → lire un texte

### Recherche dans le JORF
```
searchUsingPOST(fond: "JORF", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-cles>", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "PUBLICATION_DATE_DESC", typePagination: "DEFAUT"
})
```

### Texte par NOR
```
getJoWithNorUsingPOST(nor: "<NOR>")
```

## Reponse attendue

- Liste des textes avec : titre, nature (loi, decret, arrete...), date de publication
- Pour un texte consulte : contenu complet, NOR, numero
- Contexte : entree en vigueur, textes abroges le cas echeant
