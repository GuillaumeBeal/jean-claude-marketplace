---
name: legifrance-mcp
description: "Recherche et consultation de textes juridiques français via le MCP Légifrance. Déclencher dès qu'une question porte sur le droit français : article de code, loi, décret, arrêté, jurisprudence, convention collective, référence juridique (NOR, ECLI, IDCC). Couvre : codes (civil, pénal, commerce, travail...), LODA, JORF, JURI, CETAT, KALI, CNIL, circulaires, accords, dossiers législatifs, débats parlementaires. Patterns : 'article L.xxx du code de...', 'loi n°...', 'décret du...', 'jurisprudence...', 'convention collective...', 'IDCC...'. Aussi pour la veille juridique, l'exploration conceptuelle via le graphe de connaissances, et la vérification de citations."
---

# Légifrance MCP — Recherche Juridique Française

Skill pour cabinets d'avocats et juristes professionnels. Accès au droit français via le MCP Légifrance.

**Fichiers de référence à consulter selon le besoin :**
- `references/tools-catalog.md` — Catalogue complet des 30 outils MCP avec signatures exactes
- `references/search-parameters.md` — Paramètres de `search_texts`, fonds, filtres, tris, pièges à éviter
- `references/veille-juridique.md` — Workflow de veille, mots-clés par domaine, stratégies par durée
- `references/advanced-patterns.md` — Patterns avancés : concordance, historique, vérification, graph-first
- `references/citation-format.md` — Formats de citation professionnels (français et international)
- `references/error-handling.md` — Diagnostic d'erreurs, fallbacks, messages utilisateur

## Principes fondamentaux

1. **Toujours vérifier avant d'affirmer** : ne jamais citer un article de mémoire. Le droit évolue.
2. **Citer les sources au format professionnel** : voir `references/citation-format.md`.
3. **Date de consultation** : format YYYY-MM-DD, date du jour sauf si version historique demandée.
4. **Enchaîner les appels** : un premier identifie le texte, un second récupère le contenu.
5. **Graph-first quand le sujet est conceptuel** : utiliser `query_legal_graph` ou `get_concept_network` AVANT de lancer une recherche textuelle. Si le graphe a des résultats pertinents, utiliser les IDs retournés pour un accès direct.
6. **`search_texts` est TOUJOURS préférable** à `cross_search_suggest` (qui ne fait que de l'autocomplétion).
7. **Facettes de date strictes** : chaque fond a sa propre facette. Voir table ci-dessous.
8. **Préférer `get_filtered_table_of_contents`** à `get_table_of_contents` pour éviter les réponses de 3+ MB.

## Facettes de date par fond — OBLIGATOIRE

**Utiliser une mauvaise facette provoque une erreur 500. Toujours consulter cette table.**

| Fond | Facette de date | Format | Pièges |
|------|----------------|--------|--------|
| `JORF` | `DATE_PUBLICATION` ou `DATE_SIGNATURE` | `dates` range | ~~`DATE_PUBLI`~~ → 500 ! |
| `LODA_DATE` / `LODA_ETAT` | `DATE_SIGNATURE` | `dates` range | |
| `CODE_DATE` | `DATE_VERSION` | **`singleDate` uniquement !** | ~~`dates` range~~ → 500 ! |
| `CETAT` / `JURI` / `JUFI` / `CONSTIT` | `DATE_DECISION` | `dates` range | |
| `KALI` | `DATE_SIGNATURE` ou `DATE_PUBLICATION` | `dates` range | ~~`DATE_DECISION`~~ → 500 ! |
| `CNIL` | `DATE_DELIB` | `dates` range | ~~`DATE_DECISION`~~ → 500 ! |
| `ACCO` | `DATE_SIGNATURE` ou `DATE_DIFFUSION` | `dates` range | ~~`DATE_DECISION`~~ → 500 ! |
| `CIRC` | `DATE_SIGNATURE` | `dates` range | |

Détails complets : `references/search-parameters.md`

## Workflows principaux

### Cas 1 : Article d'un code — `get_article`

```
get_article(textId: "<LEGITEXT du code>", num: "<numéro>")
```

Formats validés : `"1240"`, `"L225-1"`, `"R123-4"`, `"D456-7"`, `"L1234-5"` — SANS point.

Codes les plus fréquents (liste complète dans `references/tools-catalog.md`) :

| Code | LEGITEXT |
|------|----------|
| Code civil | `LEGITEXT000006070721` |
| Code pénal | `LEGITEXT000006070719` |
| Code du travail | `LEGITEXT000006072050` |
| Code de commerce | `LEGITEXT000005634379` |
| CGI | `LEGITEXT000006069577` |
| Code environnement | `LEGITEXT000006074220` |
| Code monétaire et financier | `LEGITEXT000006072026` |
| Code de la consommation | `LEGITEXT000006069565` |
| Code de la propriété intellectuelle | `LEGITEXT000006069414` |
| Code de la sécurité sociale | `LEGITEXT000006073189` |

**Fallback** si non trouvé :
1. Essayer sans préfixe (`"225-1"` au lieu de `"L225-1"`)
2. Si toujours rien : `search_texts` avec `typeChamp: "NUM_ARTICLE"` dans le fond `CODE_DATE`
3. Voir `references/error-handling.md` pour les cas limites.

**Résultat** : vérifier `etat` (VIGUEUR/ABROGE), `texte`, `lienConcordes` (ancien→nouveau numéro), `articleVersions`.

**Enrichissement optionnel** (recommandé pour les questions de fond) :
- `get_article_context(article_id: "<LEGIARTI>")` → concepts associés, décisions citantes, domaine juridique
- `get_article_links(articleId: "<LEGIARTI>", linkType: "all")` → concordance + citations + fiches service-public.fr

### Cas 2 : Loi par nom ou sujet — `search_texts`

**Les lois sont indexées par titre officiel, PAS par nom courant.** Traduire d'abord :
- Loi Sapin 2 → `"transparence corruption modernisation"`
- Loi Pacte → `"croissance transformation entreprises"`
- Loi Climat → `"climat résilience"`
- Loi Egalim → `"agriculture alimentation"`

```json
search_texts(fond: "LODA_DATE", recherche: {
  champs: [{typeChamp: "TITLE", criteres: [{
    typeRecherche: "UN_DES_MOTS", valeur: "mots-clés titre officiel", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

Vérifier le champ `appellations` dans le résultat pour confirmer le bon texte.

Puis `get_law_decree(textId: "<LEGITEXT...>", date: "<date du jour>")` pour le contenu.

### Cas 3 : Vérifier une référence

| Type de référence | Outil |
|-------------------|-------|
| Article d'un code | `get_article(textId: "...", num: "...")` |
| Loi n° 2016-1691 | `search_texts` fond=LODA_DATE, typeChamp=NUM |
| NOR: JUSC1732516D | `get_jorf_text(nor: "JUSC1732516D")` |
| ECLI:FR:CCASS:... | `search_texts` fond=JURI, typeChamp=ECLI |
| IDCC 1234 | `get_convention_container(idcc: "1234")` |
| N° de pourvoi | `search_texts` fond=JURI, typeChamp=NUM_AFFAIRE |

### Cas 4 : Jurisprudence — `search_texts`

```json
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "mots-clés", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "2024-01-01", end: "2026-04-06"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "DATE_DESC", typePagination: "DEFAUT"
})
```

Puis `get_jurisprudence(textId: "JURITEXT...")` pour le texte complet.
Optionnel : `get_jurisprudence_classification(textId: "JURITEXT...")` pour le plan de classement.

Utiliser `CETAT` pour le Conseil d'État, `CONSTIT` pour le Conseil constitutionnel.

### Cas 5 : Convention collective

**Volume important** — toujours naviguer par étapes :
1. `get_convention_container(idcc: "1979")` → structure
2. `get_convention_text(id: "KALISCTA...", lookupBy: "section")` → section ciblée
3. `get_convention_text(id: "KALIARTI...", lookupBy: "article")` → article précis

IDCC courants : 1979 (HCR), 3248 (Métallurgie), 1486 (Syntec), 2098 (Prestataires), 44 (Chimie), 176 (Pharma), 2120 (Banque), 2691 (Assurance).

Pour chercher un IDCC inconnu : `list_conventions(titre: "<nom>")`.

### Cas 6 : Textes récents au JO

`get_last_journals(nbElement: 5)` pour les derniers JO.

⚠ **Attention** : la réponse est très volumineuse (structure complète de chaque JO). Limiter `nbElement` à 3-5.

Recherche dans le JORF avec filtre de date :
```json
search_texts(fond: "JORF", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "mots-clés", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_PUBLICATION", dates: {start: "2025-01-01", end: "2026-04-06"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "PUBLICATION_DATE_DESC", typePagination: "DEFAUT"
})
```
⚠ Utiliser `DATE_PUBLICATION` (pas `DATE_PUBLI`).

### Cas 7 : Délibérations CNIL — `search_texts`

```json
search_texts(fond: "CNIL", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "sanction données personnelles", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DELIB", dates: {start: "2025-01-01", end: "2026-04-06"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```
⚠ Utiliser `DATE_DELIB` (pas `DATE_DECISION`). Puis `get_cnil_text(textId: "CNILTEXT...")`.

### Cas 8 : Exploration conceptuelle — Graph-first

Quand la question est thématique ("quels sont les textes sur la responsabilité civile du fait des algorithmes ?") :

1. `query_legal_graph(concept: "responsabilité civile algorithme")` → articles, décisions, textes connus
2. `get_concept_network(concept: "responsabilité civile", depth: 2)` → concepts voisins
3. `list_graph_domains()` → vue d'ensemble des domaines indexés
4. Si le graphe retourne des IDs → accès direct via `get_article`, `get_jurisprudence`, etc.
5. Sinon → `search_texts` en fallback

### Cas 9 : Dossier législatif et travaux préparatoires

1. Trouver le dossier : `list_texts(category: "dossiers_legislatifs", extra_params: {"legislatureId": 17, "type": "LOI_PUBLIEE"})` ou rechercher la loi d'abord via `search_texts`
2. Consulter : `get_dossier_legislatif(id: "JORFDOLE...")`
3. Débats : `get_debate(id: "<ID>")` pour les débats parlementaires associés

### Cas 10 : Version historique / droit applicable à une date

1. `get_article(textId: "...", num: "...")` → obtenir le LEGIARTI et CID actuels
2. `search_nearest_version(cidText: "<LEGITEXT>", date: "2012-06-15")` → version du code à cette date
3. `get_text_version(textCid: "<LEGITEXT>", dateConsult: "2025-01-01", startYear: 2020, endYear: 2025)` → historique chronologique
4. `search_nearest_version(cidText: "<LEGITEXT>", date: "...", mode: "article_versions", articleId: "<LEGIARTI>")` → toutes versions d'un article

### Cas 11 : Navigation dans un code — Table des matières

**Toujours utiliser `get_filtered_table_of_contents`** (pas `get_table_of_contents` qui retourne des MB) :

```
get_filtered_table_of_contents(
  textId: "LEGITEXT000006070721",
  date: "2026-04-06",
  max_depth: 2,
  section_filter: "responsabilité"
)
```

Puis naviguer dans la section : `get_code_content(textId: "...", date: "...", sctCid: "LEGISCTA...")`.

### Cas 12 : Accords d'entreprise

1. `suggest_siret(searchText: "Société Générale")` → trouver le SIRET
2. `search_texts(fond: "ACCO", ...)` avec filtre IDCC ou mots-clés
3. `get_acco_text(id: "ACCOTEXT...")` pour le contenu

## Bonnes pratiques

- **Accès direct > Recherche** quand on a l'identifiant
- **Fond ciblé > ALL** : utiliser CODE_DATE, JURI, LODA_DATE plutôt que ALL
- **TOUS_LES_MOTS > UN_DES_MOTS** pour la précision, basculer si trop peu de résultats
- **TITLE > ALL** quand on cherche un texte par son intitulé
- **pageSize=10** pour commencer, augmenter si nécessaire
- **`get_filtered_table_of_contents` > `get_table_of_contents`** pour éviter l'explosion de contexte
- **`get_last_journals(3)` max** pour ne pas saturer le contexte (chaque JO = ~35 KB)
- Toujours vérifier `etat`, signaler abrogations et modifications récentes
- Source complète dans la réponse : article, code, version, identifiant
- En cas d'erreur : consulter `references/error-handling.md` avant de retenter

## Format de réponse pour un cabinet

Quand on répond à un avocat ou juriste, structurer la réponse ainsi :

1. **Réponse directe** à la question posée (synthèse en 2-3 phrases)
2. **Texte de référence** (contenu de l'article ou extrait de la décision)
3. **Contexte juridique** (position dans le code, état, historique si pertinent)
4. **Sources** au format citation professionnel (voir `references/citation-format.md`)
5. **Points d'attention** le cas échéant (abrogation, modification récente, réforme en cours)
