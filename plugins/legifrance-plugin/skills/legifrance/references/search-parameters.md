# Paramètres de searchUsingPOST

## Fonds de recherche

| Valeur | Description |
|--------|-------------|
| `ALL` | Tous les fonds (recherche large) |
| `CODE_DATE` | Codes par date de version |
| `CODE_ETAT` | Codes par état juridique |
| `LODA_DATE` | Lois/Ordonnances/Décrets/Arrêtés par date |
| `LODA_ETAT` | LODA par état juridique |
| `JORF` | Journal Officiel |
| `JURI` | Jurisprudence judiciaire (Cass.) |
| `CETAT` | Jurisprudence administrative (CE) |
| `CONSTIT` | Conseil constitutionnel |
| `KALI` | Conventions collectives |
| `CNIL` | Délibérations CNIL |
| `CIRC` | Circulaires |
| `ACCO` | Accords d'entreprise |

## Types de recherche (typeRecherche)

| Valeur | Usage |
|--------|-------|
| `UN_DES_MOTS` | Au moins un mot trouvé — recherche large |
| `TOUS_LES_MOTS_DANS_UN_CHAMP` | Tous les mots présents (+ proximité optionnelle) — recherche précise |
| `EXACTE` | Expression exacte entre guillemets |
| `AUCUN_DES_MOTS` | Exclure des mots |
| `AUCUNE_CORRESPONDANCE_A_CETTE_EXPRESSION` | Exclure une expression |

## Types de champs (typeChamp)

| Valeur | Usage |
|--------|-------|
| `ALL` | Tous les champs (défaut) |
| `TITLE` | Titre du texte — préférer pour chercher une loi par intitulé |
| `NUM` | Numéro du texte (ex: "2016-1691") |
| `NOR` | Numéro NOR |
| `NUM_ARTICLE` | Numéro d'article |
| `ARTICLE` | Contenu d'un article |
| `ECLI` | Identifiant ECLI (jurisprudence) |
| `NUM_AFFAIRE` | Numéro de pourvoi / d'affaire |
| `VISA` | Visas d'un texte |
| `ABSTRATS` | Résumés (jurisprudence) |

## Filtres courants

```json
// Filtre par nature de texte
{"facette": "NATURE", "valeurs": ["LOI", "DECRET", "ORDONNANCE", "ARRETE"]}

// Filtre par date de signature (LODA)
{"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}

// Filtre par date de décision (JURI/CETAT)
{"facette": "DATE_DECISION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}

// Filtre par état juridique
{"facette": "ETAT", "valeurs": ["VIGUEUR"]}

// Filtre par juridiction (JURI)
{"facette": "JURIDICTION", "valeurs": ["COUR_CASSATION"]}

// Filtre par formation (JURI)
{"facette": "CASSATION_FORMATION", "valeurs": ["CHAMBRE_COMMERCIALE"]}

// Filtre publication au bulletin (JURI) — pour ne garder que les arrêts publiés
{"facette": "CASSATION_TYPE_PUBLICATION_BULLETIN", "valeurs": ["T"]}
```

## Tris (sort)

| Valeur | Usage |
|--------|-------|
| `PERTINENCE` | Par pertinence (défaut recommandé) |
| `SIGNATURE_DATE_DESC` | Par date de signature décroissante (LODA) |
| `SIGNATURE_DATE_ASC` | Par date de signature croissante |
| `PUBLICATION_DATE_DESC` | Par date de publication décroissante (JORF) |
| `DATE_DECISION_DESC` | Par date de décision décroissante (JURI/CETAT) |

## Structure complète d'un appel

```json
{
  "fond": "LODA_DATE",
  "recherche": {
    "champs": [{
      "typeChamp": "ALL",
      "criteres": [{
        "typeRecherche": "TOUS_LES_MOTS_DANS_UN_CHAMP",
        "valeur": "mots clés",
        "operateur": "ET",
        "proximite": 3
      }],
      "operateur": "ET"
    }],
    "filtres": [
      {"facette": "NATURE", "valeurs": ["LOI", "DECRET"]},
      {"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}},
      {"facette": "ETAT", "valeurs": ["VIGUEUR"]}
    ],
    "operateur": "ET",
    "pageNumber": 1,
    "pageSize": 10,
    "sort": "PERTINENCE",
    "typePagination": "DEFAUT"
  }
}
```

## Interpréter les résultats

**Champs essentiels :**
- `etat` : VIGUEUR / ABROGE / MODIFIE / VIGUEUR_DIFF (entrée en vigueur future)
- `dateDebut` / `dateFin` : timestamps en millisecondes
- `texte` / `texteHtml` : contenu
- `lienConcordes` : correspondance ancien↔nouveau numéro (ex: art. 1382 → 1240)
- `lienModifications` : textes modificateurs
- `articleVersions` : historique complet
- `fullSectionsTitre` : position dans la structure du code
- `appellations` : noms courants (ex: "loi Sapin 2")
- `nor` : numéro NOR unique
- `resumePrincipal` : résumé (jurisprudence)
