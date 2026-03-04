---
name: convention
description: "Consulter une convention collective par IDCC ou par nom (Syntec, HCR, Metallurgie...). Navigation dans la structure : conteneur, sections, articles."
---

# /convention — Consultation de convention collective

Acces aux conventions collectives francaises via le MCP Legifrance.

## Workflow

### Par IDCC (numero)
1. `displayKaliContByIdccUsingPOST(id: "<IDCC>")` → structure du conteneur
2. `displayKaliSectionUsingPOST(id: "KALISCTA...")` → section ciblee
3. `displayKaliArticleUsingPOST(id: "KALIARTI...")` → article precis

### Par nom
1. Resoudre le nom vers l'IDCC (voir table ci-dessous)
2. Suivre le workflow par IDCC

### Recherche thematique dans une CC
```
searchUsingPOST(fond: "KALI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-cles>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "IDCC", valeurs: ["<IDCC>"]}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

## IDCC courants

| Convention | IDCC |
|------------|------|
| Syntec / Bureaux d'etudes | 1486 |
| HCR (Hotels, Cafes, Restaurants) | 1979 |
| Metallurgie | 3248 |
| Prestataires de services | 2098 |
| Chimie | 44 |
| Pharmacie d'officine | 176 |
| Batiment ouvriers (- 10 salaries) | 1596 |
| Batiment ouvriers (+ 10 salaries) | 1597 |
| Commerce de detail alimentaire | 2216 |
| Transport routier | 16 |
| Banque | 2120 |
| Assurance | 2691 |
| Commerce de gros | 573 |

Pour trouver un IDCC inconnu : `listConventionsUsingPOST(titre: "<nom>")`.

## Important

- Les CC sont volumineuses : toujours naviguer par etapes (conteneur → section → article)
- Ne jamais tenter de charger une CC entiere d'un coup
- Preciser la section pertinente pour la question posee

## Reponse attendue

- Nom complet de la convention collective et IDCC
- Section et article pertinents
- Texte de l'article
- Etat (en vigueur / etendu / denonce)
- Date de derniere modification
