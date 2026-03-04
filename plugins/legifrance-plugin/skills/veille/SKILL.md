---
name: veille
description: "Veille juridique sur un domaine du droit francais : actualite legislative, jurisprudence recente, textes publies au JO. Par domaine (travail, fiscal, societes, immobilier, RGPD, ESG...) et par periode."
---

# /veille — Veille juridique

Revue d'actualite juridique sur un domaine et une periode, via le MCP Legifrance.

## Workflow en 4 etapes

### 1. Cadrer
Identifier : domaine juridique, periode, niveau de detail.

### 2. Recherches paralleles sur 4 fonds

**a) Jurisprudence judiciaire (JURI)**
```
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-cles du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "<debut>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 20,
  sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```

**b) Jurisprudence administrative (CETAT)** — meme structure avec `fond: "CETAT"`

**c) Textes legislatifs (LODA)**
```
searchUsingPOST(fond: "LODA_DATE", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-cles du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_SIGNATURE", dates: {start: "<debut>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "SIGNATURE_DATE_DESC", typePagination: "DEFAUT"
})
```

**d) Journal Officiel (JORF)** — meme structure avec `fond: "JORF"`, tri `PUBLICATION_DATE_DESC`

### 3. Approfondir les resultats pertinents
- Jurisprudence : `displayJuriUsingPOST(textId: "JURITEXT...")`
- Loi/decret : `displayLawDecreeUsingPOST(textId: "LEGITEXT...", date: "...")`
- JORF : `displayJorfUsingPOST(textCid: "JORFTEXT...")`

### 4. Structurer la revue

Format de sortie :
```
## Revue d'actualite — [Domaine] — [Periode]

### Textes legislatifs et reglementaires
- [Loi/Decret n°... du ...] — [Resume]

### Jurisprudence marquante
- [Cass. com., date, n° pourvoi] — [Solution]
- [CE, date, n° affaire] — [Solution]

### Publications au Journal Officiel
- [Texte] — [Resume]

### Points d'attention
- [Changements a surveiller, entrees en vigueur prochaines]
```

## Mots-cles par domaine

| Domaine | Mots-cles | Fonds prioritaires |
|---------|-----------|-------------------|
| Droit des societes | `societe commerciale gouvernance assemblee` | JURI (com.), LODA, CODE_DATE |
| Droit du travail | `contrat travail salarie licenciement` | JURI (soc.), KALI, CODE_DATE |
| Droit fiscal | `impot fiscal contribuable` | CETAT, JURI, CODE_DATE (CGI) |
| Droit immobilier | `construction bail copropriete` | JURI (civ. 3), CETAT |
| ESG / CSRD | `durabilite reporting extra-financier` | LODA, JORF |
| Droit penal affaires | `abus biens sociaux fraude corruption` | JURI (crim.), LODA |
| RGPD / Numerique | `donnees personnelles traitement` | CNIL, CETAT, LODA |
| Propriete intellectuelle | `marque brevet auteur contrefacon` | JURI, CODE_DATE (CPI) |

## Strategies par duree

- **1 semaine** : `getLastNJoUsingPOST(nbElement: 7)` + pageSize: 10
- **1-3 mois** : pageSize: 20, filtrer par Bulletin pour la JP significative
- **6-12 mois** : decomposer par trimestres, focus arrets publies, synthese par tendances
