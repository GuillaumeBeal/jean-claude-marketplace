---
name: jo
description: "Consulter les derniers textes publiés au Journal Officiel de la République Française. Derniers JO, recherche par date ou par contenu."
---

# /jo — Journal Officiel

Accès aux publications du Journal Officiel via le MCP Légifrance.

## Workflow

### Derniers JO publiés
```
get_last_journals(nbElement: 3)
```
⚠ Limiter à 3-5 : chaque JO retourne ~35 KB de structure complète.

### Explorer un JO spécifique
1. `get_last_journals` ou recherche par date pour obtenir l'identifiant
2. `get_jorf_summary(id: "JORFCONT...")` → sommaire du JO
3. `get_jorf_text(textCid: "JORFTEXT...")` → lire un texte

### Recherche dans le JORF
```
search_texts(fond: "JORF", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_PUBLICATION", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "PUBLICATION_DATE_DESC", typePagination: "DEFAUT"
})
```
⚠ Utiliser `DATE_PUBLICATION` (pas `DATE_PUBLI` → erreur 500).

### Texte par NOR
```
get_jorf_text(nor: "<NOR>")
```

## Réponse attendue

- Liste des textes avec : titre, nature (loi, décret, arrêté...), date de publication
- Pour un texte consulté : contenu complet, NOR, numéro
- Contexte : entrée en vigueur, textes abrogés le cas échéant
- Citation : `JORF n° [numéro] du [date], texte n° [numéro]`
