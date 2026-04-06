# Catalogue des outils MCP Légifrance

## 1. RECHERCHE

| Outil | Usage |
|-------|-------|
| `search_texts` | **Recherche principale** — tous fonds, filtres, pagination, tri |
| `cross_search_suggest` | Autocomplétion de titres uniquement. NE PAS utiliser pour chercher des lois par nom courant |

## 2. CONSULTATION — Accès direct au contenu

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `get_code_content` | `textId`, `date`, `sctCid` (optionnel) | Section d'un code. **TOUJOURS fournir sctCid** sinon retourne le code entier (très volumineux) |
| `get_law_decree` | `textId`, `date` | Loi, décret, ordonnance, arrêté (fonds LODA) |
| `get_jorf_text` | `textCid` OU `nor` OU `idEliOrAlias` | Texte du Journal Officiel — 3 modes d'accès |
| `get_jorf_section` | `textCid` | Section/article d'un JORF |
| `get_jorf_summary` | `id` OU `num` OU `date` | Sommaire d'un JO (conteneur) |
| `get_jurisprudence` | `textId` OU `ancienId` | Décision judiciaire (JURI) ou administrative (CETAT) |
| `get_jurisprudence_classification` | `textId` | Plan de classement d'une décision |
| `get_convention_container` | `id` OU `idcc` | Convention collective — structure hiérarchique |
| `get_convention_text` | `id`, `lookupBy` | Contenu CC : `lookupBy="text"`, `"article"`, ou `"section"` |
| `get_cnil_text` | `textId` OU `ancienId` | Délibération CNIL |
| `get_circulaire` | `id` | Circulaire administrative |
| `get_acco_text` | `id` | Accord d'entreprise |
| `get_debate` | `id` | Débat parlementaire |
| `get_dossier_legislatif` | `id` | Dossier législatif complet |

## 3. ARTICLES — Accès direct et multi-mode

| Outil | Paramètres | Usage |
|-------|-----------|-------|
| `get_article` | **4 modes :** | **Outil principal pour les articles** |
| | `textId` + `num` | Article par numéro dans un code (ex: LEGITEXT + "L225-1") |
| | `id` seul | Article par ID technique (LEGIARTI...) |
| | `cid` seul | Toutes les versions d'un article par CID |
| | `idEliOrAlias` seul | Article par identifiant ELI |

## 4. NAVIGATION & STRUCTURE

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `get_filtered_table_of_contents` | `textId`, `date`, `max_depth`, `section_filter` | **⭐ Préférer** — TdM avec filtrage par profondeur et section |
| `get_table_of_contents` | `textId`, `date`, `nature` | TdM complète — **ATTENTION : très volumineux sur les gros codes** |
| `get_legi_part` | `textId`, `date` | Section d'un texte LEGI |
| `get_section_by_cid` | `cid` | Détail d'une section par CID |

## 5. LIENS & CONTEXTE

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `get_article_context` | `article_id` | Concepts associés, décisions citantes, domaine juridique |

**Note** : La concordance ancien↔nouveau numéro est disponible dans le champ `lienConcordes` de la réponse `get_article`. Pas besoin d'outil séparé.

## 6. GRAPHE DE CONNAISSANCES

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `query_legal_graph` | `concept`, `domaine` (opt.), `limit` | Recherche par concept — retourne articles, décisions, textes liés |
| `get_concept_network` | `concept`, `depth` (1-3), `limit` | Explorer les concepts voisins et connexions inter-domaines |
| `list_graph_domains` | `domaine` (opt.) | Vue d'ensemble des domaines indexés, ou détail d'un domaine |

## 7. VERSIONS & HISTORIQUE

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `get_text_version` | `textCid`, `dateConsult`, `startYear`, `endYear`, `elementCid` (opt.) | Versions chronologiques d'un texte ou article |
| `search_nearest_version` | `cidText`, `date`, `mode` | Version la plus proche d'une date |
| | `mode="nearest"` | Version la plus proche (défaut) |
| | `mode="canonical"` | Date de début de la version canonique |
| | `mode="article_versions"` + `articleId` | Toutes les versions d'un article |

## 8. LISTES

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `list_texts` | `category`, `pageNumber`, `pageSize`, `sort`, `extra_params` | Liste par catégorie : codes, loda, conventions, bocc, debats, dossiers_legislatifs, questions_ecrites, legislatures |
| `list_conventions` | `titre`, `idcc`, `keyWords`, `legalStatus` | Liste des conventions collectives avec filtres |
| `get_last_journals` | `nbElement` | Derniers JO publiés — **limiter à 3-5** (très volumineux) |

## 9. SUGGESTIONS

| Outil | Paramètres clés | Usage |
|-------|-----------------|-------|
| `suggest_classification` | `searchText`, `fond` (opt.) | Labels du plan de classement juridique |
| `suggest_siret` | `searchText` | SIRET et raisons sociales pour accords d'entreprise |

---

## Identifiants des principaux codes (LEGITEXT)

| Code | textId |
|------|--------|
| Code civil | `LEGITEXT000006070721` |
| Code pénal | `LEGITEXT000006070719` |
| Code du travail | `LEGITEXT000006072050` |
| Code de commerce | `LEGITEXT000005634379` |
| Code de procédure civile | `LEGITEXT000006070716` |
| Code de procédure pénale | `LEGITEXT000006071154` |
| Code de la sécurité sociale | `LEGITEXT000006073189` |
| Code général des impôts (CGI) | `LEGITEXT000006069577` |
| Code de l'environnement | `LEGITEXT000006074220` |
| Code de la santé publique | `LEGITEXT000006072665` |
| Code de la propriété intellectuelle | `LEGITEXT000006069414` |
| Code monétaire et financier | `LEGITEXT000006072026` |
| Code de l'urbanisme | `LEGITEXT000006074075` |
| Code de la construction et de l'habitation | `LEGITEXT000006074096` |
| Code de l'énergie | `LEGITEXT000023983208` |
| Code de la consommation | `LEGITEXT000006069565` |
| Constitution du 4 octobre 1958 | `LEGITEXT000006071194` |
| Livre des procédures fiscales | `LEGITEXT000006069583` |
| Code général des collectivités territoriales | `LEGITEXT000006070633` |
| Code de la justice pénale des mineurs | `LEGITEXT000039086952` |
| Code forestier (nouveau) | `LEGITEXT000025244092` |
| Code rural | `LEGITEXT000006071367` |
| Code des assurances | `LEGITEXT000006073984` |
| Code du sport | `LEGITEXT000006071318` |
| Code des postes et communications électroniques | `LEGITEXT000006070987` |

## Correspondance fonds → outil de consultation

Après un `search_texts`, utiliser l'outil correspondant au fonds du résultat :

| Fonds du résultat | Outil de consultation |
|-------------------|----------------------|
| CODE / origin: LEGI + nature: CODE | `get_code_content(textId: "...", date: "...", sctCid: "...")` |
| LODA / origin: LEGI + nature: LOI/DECRET... | `get_law_decree(textId: "...", date: "...")` |
| JORF | `get_jorf_text(textCid: "JORFTEXT...")` |
| JURI / CETAT | `get_jurisprudence(textId: "JURITEXT...")` |
| CONSTIT | `get_jurisprudence(textId: "CONSTEXT...")` |
| KALI | `get_convention_text(id: "KALITEXT...", lookupBy: "text")` |
| CNIL | `get_cnil_text(textId: "CNILTEXT...")` |
| CIRC | `get_circulaire(id: "...")` |
| ACCO | `get_acco_text(id: "ACCOTEXT...")` |
