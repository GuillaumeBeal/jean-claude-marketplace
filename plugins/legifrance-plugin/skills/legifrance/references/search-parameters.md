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

## Filtre de date par fond — REFERENCE OBLIGATOIRE

**CRITIQUE : chaque fond a ses propres facettes de date. Utiliser une mauvaise facette provoque une erreur 500 de l'API.**

| Fond | Facette de date | Format | Erreurs fréquentes |
|------|----------------|--------|-------------------|
| `JORF` | `DATE_PUBLICATION` | `dates` range | ~~`DATE_PUBLI`~~ → 500 ! |
| `JORF` | `DATE_SIGNATURE` | `dates` range | |
| `LODA_DATE` | `DATE_SIGNATURE` | `dates` range | |
| `LODA_ETAT` | `DATE_SIGNATURE` | `dates` range | |
| `CODE_DATE` | `DATE_VERSION` | **`singleDate` uniquement** | ~~`dates` range~~ → 500 ! |
| `CETAT` | `DATE_DECISION` | `dates` range | |
| `JURI` | `DATE_DECISION` | `dates` range | |
| `JUFI` | `DATE_DECISION` | `dates` range | |
| `CONSTIT` | `DATE_DECISION` | `dates` range | |
| `KALI` | `DATE_SIGNATURE` | `dates` range | ~~`DATE_DECISION`~~ → 500 ! |
| `KALI` | `DATE_PUBLICATION` | `dates` range | |
| `CNIL` | `DATE_DELIB` | `dates` range | ~~`DATE_DECISION`~~ → 500 ! |
| `ACCO` | `DATE_SIGNATURE` | `dates` range | ~~`DATE_DECISION`~~ → 500 ! |
| `ACCO` | `DATE_DIFFUSION` | `dates` range | |
| `CIRC` | `DATE_SIGNATURE` | `dates` range | |

### Formats de filtre de date

**Format `dates` range** (la plupart des fonds) :
```json
{"facette": "DATE_PUBLICATION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
```

**Format `singleDate`** (CODE_DATE uniquement) :
```json
{"facette": "DATE_VERSION", "singleDate": "2025-01-01"}
```

### Erreurs courantes qui provoquent des 500

1. **`DATE_PUBLI` au lieu de `DATE_PUBLICATION`** pour JORF — l'API ne reconnaît pas l'abréviation
2. **`DATE_DECISION` au lieu de `DATE_DELIB`** pour CNIL — `DATE_DECISION` est réservé à CETAT/JURI/JUFI/CONSTIT
3. **`DATE_DECISION` pour JORF, KALI, ACCO** — ces fonds n'ont pas cette facette
4. **`dates` range avec `DATE_VERSION`** pour CODE_DATE — utiliser `singleDate` à la place

## Autres filtres par fond

### JORF
```json
{"facette": "NATURE", "valeurs": ["LOI", "DECRET", "ORDONNANCE", "ARRETE"]}
{"facette": "DATE_PUBLICATION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "NOR", "valeurs": ["JUSC1732516D"]}
```

### LODA_DATE / LODA_ETAT
```json
{"facette": "NATURE", "valeurs": ["LOI", "DECRET", "ORDONNANCE", "ARRETE"]}
{"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "ETAT", "valeurs": ["VIGUEUR"]}
```

### CODE_DATE
```json
{"facette": "DATE_VERSION", "singleDate": "2025-01-01"}
{"facette": "NOM_CODE", "valeurs": ["Code civil"]}
```

### JURI
```json
{"facette": "DATE_DECISION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "JURIDICTION_JUDICIAIRE", "valeurs": ["COUR_CASSATION"]}
{"facette": "CASSATION_FORMATION", "valeurs": ["CHAMBRE_COMMERCIALE"]}
{"facette": "CASSATION_TYPE_PUBLICATION_BULLETIN", "valeurs": ["T"]}
{"facette": "NUM_AFFAIRE", "valeurs": ["21-12.345"]}
{"facette": "ECLI", "valeurs": ["ECLI:FR:CCASS:2021:..."]}
```

### CETAT
```json
{"facette": "DATE_DECISION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "JURIDICTION_NATURE", "valeurs": ["CONSEIL_ETAT"]}
```

### CONSTIT
```json
{"facette": "DATE_DECISION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "NATURE_CONSTIT", "valeurs": ["QPC", "DC"]}
```

### CNIL
```json
{"facette": "DATE_DELIB", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "TYPE", "valeurs": ["SANCTION"]}
{"facette": "NATURE_DELIB", "valeurs": ["DELIBERATION"]}
```

### KALI
```json
{"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "IDCC", "valeurs": ["1486"]}
```

### ACCO
```json
{"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "DATE_DIFFUSION", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "IDCC", "valeurs": ["1486"]}
```

### CIRC
```json
{"facette": "DATE_SIGNATURE", "dates": {"start": "2024-01-01", "end": "2026-03-01"}}
{"facette": "NOR", "valeurs": ["..."]}
```

### ALL
```json
{"facette": "FOND", "valeurs": ["JURI", "CETAT"]}
```

## Tris (sort) par fond

| Fond | Tris disponibles |
|------|-----------------|
| `JORF` / `LODA_DATE` / `LODA_ETAT` | `PERTINENCE`, `SIGNATURE_DATE_DESC`, `SIGNATURE_DATE_ASC`, `PUBLICATION_DATE_DESC`, `PUBLICATION_DATE_ASC` |
| `CODE_DATE` / `CODE_ETAT` | `PERTINENCE`, `SIGNATURE_DATE_DESC`, `SIGNATURE_DATE_ASC`, `PUBLICATION_DATE_DESC`, `PUBLICATION_DATE_ASC` |
| `CETAT` / `JURI` / `JUFI` / `CONSTIT` | `PERTINENCE`, `DATE_DESC`, `DATE_ASC` |
| `CNIL` | `PERTINENCE`, `DATE_DECISION_DESC`, `DATE_DECISION_ASC` |
| `KALI` | `PERTINENCE`, `MODIFICATION_DATE_DESC`, `SIGNATURE_DATE_DESC`, `SIGNATURE_DATE_ASC` |
| `ACCO` | `PERTINENCE`, `DATE_DESC`, `DATE_ASC` |
| `CIRC` | `PERTINENCE`, `SIGNATURE_DATE_DESC`, `SIGNATURE_DATE_ASC`, `PUBLI_DATE_DESC`, `PUBLI_DATE_ASC` |
| `ALL` | `PERTINENCE` |

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
