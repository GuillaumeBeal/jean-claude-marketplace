# Outils MCP et Identifiants des Codes

## Outils MCP disponibles (préfixés `Légifrance:`)

### 1. RECHERCHE
| Outil | Usage |
|-------|-------|
| `searchUsingPOST` | **Recherche principale** — tous fonds, filtres, pagination, tri |
| `crossSearchUsingPOST` | Autocomplétion de titres uniquement. NE PAS utiliser pour chercher des lois par nom courant |

### 2. CONSULTATION
| Outil | Fonds | Usage |
|-------|-------|-------|
| `displayCodeUsingPOST` | CODE | Section d'un code |
| `displayCodeTableOfContentsUsingPOST` | CODE | Table des matières |
| `displayLawDecreeUsingPOST` | LODA | Loi, décret, ordonnance, arrêté |
| `displayJorfUsingPOST` | JORF | Texte du Journal Officiel |
| `displayJorfPartUsingPOST` | JORF | Section/article d'un JORF |
| `displayJuriUsingPOST` | JURI | Décision judiciaire |
| `displayKaliContUsingPOST` | KALI | Convention collective (conteneur) |
| `displayKaliContByIdccUsingPOST` | KALI | Convention collective par IDCC |
| `displayKaliTextUsingPOST` | KALI | Texte d'une CC |
| `displayKaliArticleUsingPOST` | KALI | Article d'une CC |
| `displayKaliSectionUsingPOST` | KALI | Section d'une CC |
| `displayCnilUsingPOST` | CNIL | Délibération CNIL |
| `displayCirculaireUsingPOST` | CIRC | Circulaire |
| `displayAccoUsingPOST` | ACCO | Accord d'entreprise |

### 3. ARTICLES — Accès direct
| Outil | Usage |
|-------|-------|
| `getArticleWithIdAndNumUsingPOST` | **⭐ Le plus utile** — article par numéro dans un code |
| `getArticleUsingPOST` | Article par ID LEGIARTI |
| `getArticleByCidUsingPOST` | Toutes versions d'un article par CID |
| `getArticleWithIdEliOrAliasUsingPOST` | Article par identifiant ELI |
| `searchCanonicalArticleVersionUsingPOST` | Historique des versions |

### 4. NAVIGATION
| Outil | Usage |
|-------|-------|
| `displayTextTableOfContentsUsingPOST` | TdM de tout texte CODE ou LODA |
| `displayLegiPartUsingPOST` | Partie d'un texte LEGI |
| `getSectionByCidUsingPOST` | Détail d'une section |
| `displayRelatedLinksArticleUsingPOST` | Liens relatifs d'un article |
| `displayConcordanceLinksArticleUsingPOST` | Liens de concordance (ancien↔nouveau) |
| `displayServicePublicLinksArticleUsingPOST` | Fiches service-public.fr liées |

### 5. VERSIONS & HISTORIQUE
| Outil | Usage |
|-------|-------|
| `getVersionByTextCidUsingPOST` | Versions chronologiques d'un texte |
| `getVersionByTextCidAndElementCidUsingPOST` | Version spécifique d'un extrait |
| `searchNearestVersionUsingPOST` | Version la plus proche d'une date |
| `searchCanonicalVersionUsingPOST` | Version canonique |
| `hasVersionByTextCidUsingGET` | Vérifier si des versions chrono existent |

### 6. LISTES
| Outil | Usage |
|-------|-------|
| `listCodeUsingPOST` | Liste de tous les codes |
| `listLODAUsingPOST` | Liste des lois/décrets/ordonnances |
| `listConventionsUsingPOST` | Liste des conventions collectives |
| `listDossiersLegislatifsUsingPOST` | Dossiers législatifs |
| `getLastNJoUsingPOST` | Derniers JO publiés |
| `getJoWithNorUsingPOST` | Texte JO par NOR |

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

## Correspondance fonds → outil de consultation

Après un `searchUsingPOST`, utiliser l'outil correspondant au fonds :

| Fonds du résultat | Outil de consultation |
|-------------------|----------------------|
| CODE / origin: LEGI + nature: CODE | `displayCodeUsingPOST` |
| LODA / origin: LEGI + nature: LOI/DECRET... | `displayLawDecreeUsingPOST` |
| JORF | `displayJorfUsingPOST` |
| JURI | `displayJuriUsingPOST` |
| CETAT | `displayJuriUsingPOST` (même outil) |
| KALI | `displayKaliTextUsingPOST` ou `displayKaliContUsingPOST` |
| CNIL | `displayCnilUsingPOST` |
| CIRC | `displayCirculaireUsingPOST` |
| ACCO | `displayAccoUsingPOST` |
