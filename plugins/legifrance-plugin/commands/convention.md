---
name: convention
description: "Consulter une convention collective par IDCC ou par nom (Syntec, HCR, Métallurgie...). Navigation dans la structure : conteneur, sections, articles."
---

# /convention — Consultation de convention collective

Accès aux conventions collectives françaises via le MCP Légifrance.

## Workflow

### Par IDCC (numéro)
1. `get_convention_container(idcc: "<IDCC>")` → structure du conteneur
2. `get_convention_text(id: "KALISCTA...", lookupBy: "section")` → section ciblée
3. `get_convention_text(id: "KALIARTI...", lookupBy: "article")` → article précis

### Par nom
1. Résoudre le nom vers l'IDCC (voir table ci-dessous)
2. Suivre le workflow par IDCC

### Recherche thématique dans une CC
```
search_texts(fond: "KALI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "IDCC", valeurs: ["<IDCC>"]}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

## IDCC courants

| Convention | IDCC |
|------------|------|
| Syntec / Bureaux d'études | 1486 |
| HCR (Hôtels, Cafés, Restaurants) | 1979 |
| Métallurgie | 3248 |
| Prestataires de services | 2098 |
| Chimie | 44 |
| Pharmacie d'officine | 176 |
| Bâtiment ouvriers (- 10 salariés) | 1596 |
| Bâtiment ouvriers (+ 10 salariés) | 1597 |
| Commerce de détail alimentaire | 2216 |
| Transport routier | 16 |
| Banque | 2120 |
| Assurance | 2691 |
| Commerce de gros | 573 |

Pour trouver un IDCC inconnu : `list_conventions(titre: "<nom>")`.

## Important

- Les CC sont volumineuses : toujours naviguer par étapes (conteneur → section → article)
- Ne jamais tenter de charger une CC entière d'un coup
- Préciser la section pertinente pour la question posée

## Réponse attendue

- Nom complet de la convention collective et IDCC
- Section et article pertinents
- Texte de l'article
- État (en vigueur / étendu / dénoncé)
- Date de dernière modification
- Citation : `Art. [num] de la convention collective nationale [intitulé], IDCC [numéro]`
