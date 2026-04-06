---
name: veille
description: "Veille juridique sur un domaine du droit français : actualité législative, jurisprudence récente, textes publiés au JO. Par domaine (travail, fiscal, sociétés, immobilier, RGPD, ESG...) et par période."
---

# /veille — Veille juridique

Revue d'actualité juridique sur un domaine et une période, via le MCP Légifrance.

## Workflow en 5 étapes

### 1. Cadrer
Identifier : domaine juridique, période, niveau de détail.

### 2. Explorer le graphe (si sujet conceptuel)
```
query_legal_graph(concept: "<concept du domaine>", limit: 10)
```
Inclure les résultats du graphe dans la revue si pertinents.

### 3. Recherches parallèles sur 4-5 fonds

**a) Jurisprudence judiciaire (JURI)**
```
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 20,
  sort: "DATE_DESC", typePagination: "DEFAUT"
})
```

**b) Jurisprudence administrative (CETAT)** — même structure avec `fond: "CETAT"`

**c) Textes législatifs (LODA)**
```
search_texts(fond: "LODA_DATE", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_SIGNATURE", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "SIGNATURE_DATE_DESC", typePagination: "DEFAUT"
})
```

**d) Journal Officiel (JORF)** — même structure avec `fond: "JORF"`, facette `DATE_PUBLICATION`, tri `PUBLICATION_DATE_DESC`

**e) CNIL** (si domaine RGPD) — facette `DATE_DELIB`

**f) CONSTIT** (si QPC pertinentes) — facette `DATE_DECISION`

### 4. Approfondir les résultats pertinents
- `get_jurisprudence(textId: "JURITEXT...")` + `get_jurisprudence_classification(textId: "...")`
- `get_law_decree(textId: "LEGITEXT...", date: "...")`
- `get_jorf_text(textCid: "JORFTEXT...")`
- `get_cnil_text(textId: "CNILTEXT...")`

### 5. Structurer la revue

```
## Revue d'actualité — [Domaine] — [Période]

### Textes législatifs et réglementaires
- [Loi/Décret n°... du ...] — [Résumé et impact]

### Jurisprudence marquante
- [Cass. com., date, n° pourvoi, publié au Bull.] — [Solution et portée]
- [CE, date, n° affaire] — [Solution et portée]

### Publications au Journal Officiel
- [Texte] — [Résumé]

### Points d'attention
- [Changements à surveiller, entrées en vigueur prochaines]
```

## Mots-clés par domaine

| Domaine | Mots-clés | Fonds prioritaires |
|---------|-----------|-------------------|
| Droit des sociétés | `société commerciale gouvernance assemblée` | JURI (com.), LODA, CODE_DATE |
| Droit du travail | `contrat travail salarié licenciement` | JURI (soc.), KALI, CODE_DATE |
| Droit fiscal | `impôt fiscal contribuable` | CETAT, JURI, CODE_DATE (CGI) |
| Droit immobilier | `construction bail copropriété` | JURI (civ. 3e), CETAT |
| ESG / CSRD | `durabilité reporting extra-financier` | LODA, JORF |
| Droit pénal affaires | `abus biens sociaux fraude corruption` | JURI (crim.), LODA |
| RGPD / Numérique | `données personnelles traitement` | CNIL, CETAT, LODA |
| Propriété intellectuelle | `marque brevet auteur contrefaçon` | JURI, CODE_DATE (CPI) |
| Droit bancaire | `bancaire crédit prêt emprunteur` | JURI (com.), CODE_DATE (CMF) |
| Concurrence | `entente concurrence position dominante` | CETAT, JURI (com.), LODA |

## Stratégies par durée

- **1 semaine** : `get_last_journals(nbElement: 3)` + pageSize: 10
- **1-3 mois** : pageSize: 20, filtrer par Bulletin pour la JP significative
- **6-12 mois** : décomposer par trimestres, focus arrêts publiés, synthèse par tendances
