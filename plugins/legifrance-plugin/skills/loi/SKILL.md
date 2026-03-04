---
name: loi
description: "Trouver une loi francaise par nom courant (Sapin 2, Pacte, Climat, Egalim...) ou par numero (n 2016-1691). Traduit automatiquement les noms courants en titres officiels pour la recherche Legifrance."
---

# /loi — Rechercher une loi

Trouve une loi francaise a partir de son nom courant ou de son numero, via le MCP Legifrance.

## Le probleme

Les lois sont indexees par **titre officiel**, pas par nom courant. Il faut traduire :

| Nom courant | Mots-cles titre officiel |
|-------------|--------------------------|
| Loi Sapin 2 | `transparence corruption modernisation` |
| Loi Pacte | `croissance transformation entreprises` |
| Loi Climat | `climat resilience` |
| Loi Egalim | `agriculture alimentation` |
| Loi Macron | `croissance activite economique` |
| Loi El Khomri / Travail | `travail modernisation dialogue social` |
| Loi Elan | `evolution logement amenagement numerique` |
| Loi Hamon | `consommation` |
| Loi RGPD / Informatique | `informatique libertes` |
| Loi Vigilance | `vigilance societes meres` |

## Workflow

### Par nom courant
1. Traduire le nom courant en mots-cles du titre officiel
2. `searchUsingPOST(fond: "LODA_DATE", recherche: {champs: [{typeChamp: "TITLE", criteres: [{typeRecherche: "UN_DES_MOTS", valeur: "<mots-cles>", operateur: "ET"}], operateur: "ET"}], operateur: "ET", pageNumber: 1, pageSize: 10, sort: "PERTINENCE", typePagination: "DEFAUT"})`
3. Verifier le champ `appellations` dans les resultats pour confirmer
4. `displayLawDecreeUsingPOST(textId: "<LEGITEXT...>", date: "<date du jour>")` pour le contenu

### Par numero (ex: 2016-1691)
1. `searchUsingPOST(fond: "LODA_DATE", recherche: {champs: [{typeChamp: "NUM", criteres: [{typeRecherche: "EXACTE", valeur: "2016-1691", operateur: "ET"}], operateur: "ET"}], operateur: "ET", pageNumber: 1, pageSize: 5, sort: "PERTINENCE", typePagination: "DEFAUT"})`
2. `displayLawDecreeUsingPOST` pour le contenu

### Par NOR
1. `getJoWithNorUsingPOST(nor: "<NOR>")`

## Reponse attendue

- Titre officiel complet et nom courant (appellation)
- Numero et date de signature
- NOR
- Etat juridique (en vigueur, partiellement abroge...)
- Resume du contenu ou table des matieres si volumineuse
- Identifiant LEGITEXT pour consultation ulterieure
