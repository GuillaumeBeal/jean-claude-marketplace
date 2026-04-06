---
name: loi
description: "Trouver une loi française par nom courant (Sapin 2, Pacte, Climat, Egalim...) ou par numéro (n° 2016-1691). Traduit automatiquement les noms courants en titres officiels pour la recherche Légifrance."
---

# /loi — Rechercher une loi

Trouve une loi française à partir de son nom courant ou de son numéro, via le MCP Légifrance.

## Le problème

Les lois sont indexées par **titre officiel**, pas par nom courant. Il faut traduire :

| Nom courant | Mots-clés titre officiel |
|-------------|--------------------------|
| Loi Sapin 2 | `transparence corruption modernisation` |
| Loi Pacte | `croissance transformation entreprises` |
| Loi Climat | `climat résilience` |
| Loi Egalim | `agriculture alimentation` |
| Loi Macron | `croissance activité économique` |
| Loi El Khomri / Travail | `travail modernisation dialogue social` |
| Loi Elan | `évolution logement aménagement numérique` |
| Loi Hamon | `consommation` |
| Loi RGPD / Informatique | `informatique libertés` |
| Loi Vigilance | `vigilance sociétés mères` |

## Workflow

### Par nom courant
1. Traduire le nom courant en mots-clés du titre officiel
2. ```
   search_texts(fond: "LODA_DATE", recherche: {
     champs: [{typeChamp: "TITLE", criteres: [{
       typeRecherche: "UN_DES_MOTS", valeur: "<mots-clés>", operateur: "ET"
     }], operateur: "ET"}],
     operateur: "ET", pageNumber: 1, pageSize: 10,
     sort: "PERTINENCE", typePagination: "DEFAUT"
   })
   ```
3. Vérifier le champ `appellations` dans les résultats pour confirmer
4. `get_law_decree(textId: "<LEGITEXT...>", date: "<date du jour>")` pour le contenu

### Par numéro (ex: 2016-1691)
1. ```
   search_texts(fond: "LODA_DATE", recherche: {
     champs: [{typeChamp: "NUM", criteres: [{
       typeRecherche: "EXPRESSION_EXACTE", valeur: "2016-1691", operateur: "ET"
     }], operateur: "ET"}],
     operateur: "ET", pageNumber: 1, pageSize: 5,
     sort: "PERTINENCE", typePagination: "DEFAUT"
   })
   ```
2. `get_law_decree(textId: "...", date: "...")` pour le contenu

### Par NOR
1. `get_jorf_text(nor: "<NOR>")`

## Réponse attendue

- Titre officiel complet et nom courant (appellation)
- Numéro et date de signature
- NOR
- État juridique (en vigueur, partiellement abrogé...)
- Résumé du contenu ou table des matières si volumineuse
- Identifiant LEGITEXT pour consultation ultérieure
- Citation : `Loi n° [numéro] du [date] relative à [objet] (dite « [nom courant] »)`
