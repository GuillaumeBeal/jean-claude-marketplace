---
name: legifrance-mcp
description: "Recherche et consultation de textes juridiques français via le MCP Légifrance. Déclencher dès qu'une question porte sur le droit français : article de code, loi, décret, arrêté, jurisprudence, convention collective, référence juridique (NOR, ECLI, IDCC). Couvre : codes (civil, pénal, commerce, travail...), LODA, JORF, JURI, CETAT, KALI, CNIL, circulaires, accords. Patterns : 'article L.xxx du code de...', 'loi n°...', 'décret du...', 'jurisprudence...', 'convention collective...', 'IDCC...'. Aussi pour la veille juridique : actualité, dernières décisions, textes récents, évolutions législatives ou jurisprudentielles sur une période et un domaine du droit."
---

# Légifrance MCP - Recherche Juridique Française

Skill pour utiliser le MCP Légifrance. Outils directement appelables, pas besoin d'auth ni de code.

**Fichiers de référence à consulter selon le besoin :**
- `references/tools-and-codes.md` — Catalogue complet des 35+ outils MCP et identifiants des codes
- `references/search-parameters.md` — Paramètres de searchUsingPOST, fonds, filtres, types de recherche
- `references/veille-juridique.md` — Workflow de veille, mots-clés par domaine, stratégies par durée
- `references/advanced-patterns.md` — Patterns avancés : concordance, historique, vérification de citations

## Principes fondamentaux

1. **Toujours vérifier avant d'affirmer** : ne jamais citer un article de mémoire. Le droit évolue.
2. **Citer les sources** : identifiant Légifrance, numéro d'article, date de version.
3. **Date de consultation** : format YYYY-MM-DD, date du jour sauf si version historique demandée.
4. **Enchaîner les appels** : un premier identifie le texte, un second récupère le contenu.
5. **`searchUsingPOST` est TOUJOURS préférable** à `crossSearchUsingPOST` (qui ne fait que de l'autocomplétion de titres).
6. **Facettes de date strictes** : chaque fond a sa propre facette. Voir table ci-dessous.

## Facettes de date par fond — OBLIGATOIRE

**Utiliser une mauvaise facette provoque une erreur 500. Toujours consulter cette table.**

| Fond | Facette de date | Pièges courants |
|------|----------------|-----------------|
| `JORF` | `DATE_PUBLICATION` ou `DATE_SIGNATURE` | ~~`DATE_PUBLI`~~ → 500 ! |
| `LODA_DATE` / `LODA_ETAT` | `DATE_SIGNATURE` | |
| `CODE_DATE` | `DATE_VERSION` (**singleDate** uniquement !) | ~~`dates` range~~ → 500 ! |
| `CETAT` / `JURI` / `JUFI` / `CONSTIT` | `DATE_DECISION` | |
| `KALI` | `DATE_SIGNATURE` ou `DATE_PUBLICATION` | ~~`DATE_DECISION`~~ → 500 ! |
| `CNIL` | `DATE_DELIB` | ~~`DATE_DECISION`~~ → 500 ! |
| `ACCO` | `DATE_SIGNATURE` ou `DATE_DIFFUSION` | ~~`DATE_DECISION`~~ → 500 ! |
| `CIRC` | `DATE_SIGNATURE` | |

Détails complets : `references/search-parameters.md`

## Workflows principaux

### Cas 1 : Article d'un code — `getArticleWithIdAndNumUsingPOST`

```
getArticleWithIdAndNumUsingPOST(id: "<LEGITEXT du code>", num: "<numéro>")
```

Formats validés : `"1240"`, `"L225-1"`, `"R123-4"`, `"D456-7"`, `"L1234-5"` — SANS point.

Codes les plus fréquents (liste complète dans `references/tools-and-codes.md`) :

| Code | LEGITEXT |
|------|----------|
| Code civil | `LEGITEXT000006070721` |
| Code pénal | `LEGITEXT000006070719` |
| Code du travail | `LEGITEXT000006072050` |
| Code de commerce | `LEGITEXT000005634379` |
| CGI | `LEGITEXT000006069577` |
| Code environnement | `LEGITEXT000006074220` |
| Code monétaire et financier | `LEGITEXT000006072026` |

**Fallback** si non trouvé : essayer sans préfixe (`"225-1"`), puis `searchUsingPOST` avec `typeChamp: "NUM_ARTICLE"`.

**Résultat** : vérifier `etat` (VIGUEUR/ABROGE), `texte`, `lienConcordes` (ancien→nouveau numéro), `articleVersions`.

### Cas 2 : Loi par nom ou sujet — `searchUsingPOST`

**Les lois sont indexées par titre officiel, PAS par nom courant.** Traduire d'abord :
- Loi Sapin 2 → `"transparence corruption modernisation"`
- Loi Pacte → `"croissance transformation entreprises"`
- Loi Climat → `"climat résilience"`
- Loi Egalim → `"agriculture alimentation"`

```json
searchUsingPOST(fond: "LODA_DATE", recherche: {
  champs: [{typeChamp: "TITLE", criteres: [{
    typeRecherche: "UN_DES_MOTS", valeur: "mots-clés titre officiel", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

Vérifier le champ `appellations` dans le résultat pour confirmer le bon texte.

### Cas 3 : Vérifier une référence

| Type de référence | Outil |
|-------------------|-------|
| Article d'un code | `getArticleWithIdAndNumUsingPOST` |
| Loi n° 2016-1691 | `searchUsingPOST` fond=LODA_DATE, typeChamp=NUM |
| NOR: JUSC1732516D | `getJoWithNorUsingPOST` |
| ECLI:FR:CCASS:... | `searchUsingPOST` fond=JURI, typeChamp=ECLI |
| IDCC 1234 | `displayKaliContByIdccUsingPOST` |

### Cas 4 : Jurisprudence — `searchUsingPOST`

```json
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "mots-clés", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "2024-01-01", end: "2026-03-01"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```

Puis `displayJuriUsingPOST(textId: "JURITEXT...")` pour le texte complet. Utiliser `CETAT` pour le Conseil d'État.

### Cas 5 : Convention collective

**Volume important** — toujours naviguer par étapes :
1. `displayKaliContByIdccUsingPOST(id: "1979")` → structure
2. `displayKaliSectionUsingPOST(id: "KALISCTA...")` → section ciblée
3. `displayKaliArticleUsingPOST(id: "KALIARTI...")` → article précis

IDCC courants : 1979 (HCR), 3248 (Métallurgie), 1486 (Syntec), 2098 (Prestataires), 44 (Chimie), 176 (Pharma).

### Cas 6 : Textes récents au JO

`getLastNJoUsingPOST(nbElement: 5)` pour les derniers JO.

Recherche dans le JORF avec filtre de date :
```json
searchUsingPOST(fond: "JORF", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "mots-clés", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_PUBLICATION", dates: {start: "2025-01-01", end: "2026-03-01"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "PUBLICATION_DATE_DESC", typePagination: "DEFAUT"
})
```
⚠ Utiliser `DATE_PUBLICATION` (pas `DATE_PUBLI`).

### Cas 7 : Délibérations CNIL — `searchUsingPOST`

```json
searchUsingPOST(fond: "CNIL", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "sanction données personnelles", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DELIB", dates: {start: "2025-01-01", end: "2026-03-01"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```
⚠ Utiliser `DATE_DELIB` (pas `DATE_DECISION`). Puis `displayCnilUsingPOST(textId: "CNILTEXT...")`.

### Cas 8 : Version historique → voir `references/advanced-patterns.md`

### Cas 9 : Veille juridique → voir `references/veille-juridique.md`

## Bonnes pratiques

- **Accès direct > Recherche** quand on a l'identifiant
- **Fond ciblé > ALL** : utiliser CODE_DATE, JURI, LODA_DATE plutôt que ALL
- **TOUS_LES_MOTS > UN_DES_MOTS** pour la précision, basculer si trop peu de résultats
- **TITLE > ALL** quand on cherche un texte par son intitulé
- **pageSize=10** pour commencer, augmenter si nécessaire
- Toujours vérifier `etat`, signaler abrogations et modifications récentes
- Source complète dans la réponse : article, code, version, identifiant

## Exemples rapides

```
# Article 1240 Code civil
getArticleWithIdAndNumUsingPOST(id: "LEGITEXT000006070721", num: "1240")

# Loi Sapin 2
searchUsingPOST(fond: "LODA_DATE", recherche: {champs: [{typeChamp: "TITLE", criteres: [{typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "transparence corruption modernisation", operateur: "ET"}], operateur: "ET"}], operateur: "ET", pageNumber: 1, pageSize: 5, sort: "PERTINENCE", typePagination: "DEFAUT"})

# Texte par NOR
getJoWithNorUsingPOST(nor: "JUSC1732516D")

# CC HCR
displayKaliContByIdccUsingPOST(id: "1979")

# Derniers JO
getLastNJoUsingPOST(nbElement: 5)
```
