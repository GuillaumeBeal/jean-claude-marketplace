---
name: comparer
description: "Comparer deux versions d'un article ou d'un texte de loi. Utile pour identifier les modifications apportées par une réforme."
---

# /comparer — Comparaison de versions

Compare deux versions d'un article ou texte pour identifier les modifications.

## Workflow

### Comparer les versions d'un article
1. `get_article(textId: "...", num: "...")` → version actuelle, obtenir le CID
2. `get_article(cid: "<CID>")` → toutes les versions (tableau `articleVersions`)
3. `get_article(id: "<LEGIARTI version A>")` → texte version A
4. `get_article(id: "<LEGIARTI version B>")` → texte version B
5. Présenter les différences

### Alternative : historique chronologique
```
get_text_version(
  textCid: "LEGITEXT000006070721",
  dateConsult: "2026-04-06",
  startYear: 2016,
  endYear: 2026
)
```

### Version à une date précise
```
search_nearest_version(
  cidText: "LEGITEXT000006070721",
  date: "2016-09-30"
)
```
→ Retourne la version en vigueur à cette date.

### Toutes les versions d'un article
```
search_nearest_version(
  cidText: "LEGITEXT000006070721",
  date: "2026-04-06",
  mode: "article_versions",
  articleId: "LEGIARTI000032041571"
)
```

## Réponse attendue

- Texte des deux versions côte à côte
- Modifications identifiées (ajouts, suppressions, reformulations)
- Texte modificateur (loi/ordonnance/décret qui a opéré la modification)
- Date d'entrée en vigueur de chaque version
- Impact pratique du changement
