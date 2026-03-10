---
name: inpi-rne
description: >
  Recherche et consultation d'informations sur les sociétés françaises via le connecteur INPI/RNE.
  Utiliser cette skill dès que l'utilisateur mentionne : recherche de société, fiche société,
  informations entreprise, SIREN, SIRET, RNE, INPI, registre national des entreprises,
  bénéficiaires effectifs, Kbis, bilans, comptes annuels, actes déposés, statuts,
  recherche de marques, marque déposée, INPI marques, recherche de brevets, brevet français,
  propriété intellectuelle française, dessins et modèles.
  Couvre : recherche par nom ou SIREN, fiche société complète (corporate + bénéficiaires +
  documents déposés + bilans), recherche de marques/brevets/dessins.
  Destinée aux juristes, comptables et professionnels ayant besoin d'accéder rapidement
  aux données du registre national des entreprises et de la propriété intellectuelle.
---

# INPI / RNE — Recherche sociétés et propriété intellectuelle

Cette skill guide l'utilisation du connecteur INPI/RNE pour rechercher des informations
sur les sociétés françaises et la propriété intellectuelle (marques, brevets, dessins).

## Outils disponibles

Le connecteur INPI/RNE expose les outils suivants. Ils sont tous préfixés `inpi_`.

### Sociétés

| Outil | Rôle | Paramètres clés |
|---|---|---|
| `inpi_search_companies` | Rechercher des sociétés | `company_name`, `siren` (un ou plusieurs séparés par des virgules), `zip_codes`, `activity_sectors` (codes NAF), `code_category` (forme juridique) |
| `inpi_get_company` | Fiche détaillée d'une société | `siren` (9 chiffres), `date` optionnel (YYYY-MM-DD) pour un état historique |
| `inpi_get_beneficiaires` | Bénéficiaires effectifs (RBE) | `siren` |
| `inpi_get_attachments` | Liste des documents déposés (actes, bilans) | `siren` |
| `inpi_get_acte` | Contenu d'un acte (statuts, PV…) | `acte_id` (obtenu via `inpi_get_attachments`) |
| `inpi_get_bilan_saisi` | Données financières d'un bilan | `bilan_id` (obtenu via `inpi_get_attachments`) |

### Propriété intellectuelle

| Outil | Rôle | Paramètres clés |
|---|---|---|
| `inpi_search_marques` | Rechercher des marques | `query` (texte libre ou syntaxe SolR : `denomination_s`, `deposant_s`, `classes_s`…), `collections` (FR, EU, WO) |
| `inpi_get_marque` | Détail d'une marque | `numero` (ex : '4859654') |
| `inpi_search_brevets` | Rechercher des brevets | `query` (texte libre ou SolR : `deposant_s`, `titreFr_t`, `cpc_s`…), `collections` (FR, EP, WO, CCP) |
| `inpi_get_brevet` | Détail d'un brevet | `numero` (ex : 'FR3098765') |
| `inpi_search_dessins` | Rechercher des dessins/modèles | `query`, `collections` (FR, WO) |
| `inpi_get_dessin` | Détail d'un dessin/modèle | `numero` |

## Workflows

### 1. Recherche de société

Quand l'utilisateur donne un **nom de société** :
1. Appeler `inpi_search_companies` avec `company_name`
2. Présenter les résultats sous forme de liste concise : dénomination, SIREN, forme juridique, siège, code NAF
3. **Attention à la désambiguïsation** : les grands groupes ont souvent plusieurs entités au RNE
   (ex : "Dassault Systèmes" peut retourner la SE, la SAS, une filiale internationale…).
   Quand plusieurs résultats apparaissent, aider l'utilisateur à identifier la bonne entité :
   - Privilégier la société mère (capital le plus élevé, forme SA/SE)
   - Signaler les entités qui semblent être des filiales ou des structures dédiées
   - En cas de doute, demander à l'utilisateur de confirmer
4. Si un seul résultat pertinent (ou après sélection), enchaîner directement avec la fiche complète
   — ne pas se contenter de "proposer", y aller. L'utilisateur qui demande une fiche société veut la fiche, pas une liste de résultats.

Quand l'utilisateur donne un **numéro SIREN** :
1. Appeler directement `inpi_get_company` avec le SIREN — ne pas passer par la recherche
2. Enchaîner avec la fiche complète (workflow 2 ci-dessous)

**SIREN vs SIRET** : le connecteur travaille avec des SIREN (9 chiffres). Si l'utilisateur donne un SIRET (14 chiffres), extraire les 9 premiers chiffres pour obtenir le SIREN.

### 2. Fiche société complète

C'est le workflow le plus fréquent. L'objectif est de fournir une fiche riche et exploitable
en un minimum d'échanges — les juristes et comptables veulent des données, pas des allers-retours.

**Étape 1 — Collecte en parallèle :**

Toujours lancer ces 3 appels simultanément :
```
┌─ inpi_get_company(siren)          → Infos corporate + dirigeants + établissements
├─ inpi_get_beneficiaires(siren)    → Bénéficiaires effectifs
└─ inpi_get_attachments(siren)      → Documents déposés (actes + bilans)
```

**Étape 2 — Faut-il aller chercher le bilan ?**

Oui, si l'une de ces conditions est remplie :
- L'utilisateur a mentionné les comptes, le bilan, les chiffres, le CA, le résultat, la situation financière
- L'utilisateur a demandé une fiche « complète » ou « détaillée »
- Le contexte implique un besoin financier (due diligence, analyse d'un prospect, vérification de solvabilité)

Dans ce cas, dès que `inpi_get_attachments` a répondu, identifier le **bilan complet le plus récent**
(type "C" = complet, à préférer au type "K" = simplifié) et appeler `inpi_get_bilan_saisi(bilan_id)`.

Si l'utilisateur n'a pas demandé de données financières, ne pas consulter le bilan — se contenter
de lister les exercices disponibles avec leurs dates de clôture.

**Étape 3 — Présentation structurée :**

**Identification**
- Dénomination, SIREN, forme juridique
- Capital social
- Adresse du siège
- Code NAF / activité
- Date d'immatriculation

**Dirigeants**
- Liste des dirigeants et représentants légaux (nom, qualité, date de nomination)
- Traduire les codes de rôle en libellés clairs :
  rôle 51 = Président, 53 = Directeur Général, 65 = Administrateur,
  71 = Commissaire aux comptes, 73 = Président-Directeur Général

**Bénéficiaires effectifs**
- Liste des BE déclarés (nom, date de naissance, nationalité, modalités de contrôle, % de détention)
- Si aucun BE retourné, l'indiquer clairement (fréquent pour les sociétés cotées ou les SE)

**Établissements**
- Nombre total d'établissements ouverts
- Siège principal (adresse, SIRET)
- Ne lister les établissements secondaires que si l'utilisateur le demande ou s'il y en a peu (< 5)

**Documents déposés**
- Nombre total d'actes et de bilans
- Résumer les 5 actes les plus récents (date)
- Lister les 3 derniers exercices de bilans (date de clôture, type C/K/S)

**Données financières** (si le bilan a été consulté)
- Présenter les chiffres clés du dernier exercice :
  Chiffre d'affaires, résultat d'exploitation, résultat net, capitaux propres, total bilan, effectif moyen
- Si le bilan contient les données N-1, ajouter la comparaison et les variations en %
- Attention aux unités : les montants du bilan INPI sont en euros (pas en milliers).
  Un CA de 2 435 000 000 = 2,4 milliards d'euros. Formater lisiblement (ex : "2,4 Md€").

### 3. Consultation de bilan

Quand l'utilisateur demande les comptes / le bilan d'une société :
1. Si on n'a pas encore les attachments, appeler `inpi_get_attachments(siren)`
2. Identifier le bilan le plus récent (ou celui demandé par l'utilisateur)
3. Appeler `inpi_get_bilan_saisi(bilan_id)`
4. Présenter les chiffres clés de manière lisible :
   - Chiffre d'affaires, résultat net, capitaux propres, total bilan
   - Effectif moyen si disponible
   - Comparer avec l'exercice précédent si les données sont présentes

### 4. Recherche de marques

1. Appeler `inpi_search_marques` avec la query de l'utilisateur
2. Par défaut rechercher dans les collections FR (marques françaises). Ajouter EU et WO si l'utilisateur demande une recherche élargie
3. Présenter : dénomination, numéro, déposant, classes de Nice, statut, dates clés
4. Si l'utilisateur veut le détail d'une marque, appeler `inpi_get_marque(numero)`

**Astuces SolR pour affiner** :
- Par déposant : `deposant_s:"L'Oreal"`
- Par classe de Nice : `classes_s:35`
- Par date : `dateDepot_dt:[2024-01-01T00:00:00Z TO *]`

### 5. Recherche de brevets

1. Appeler `inpi_search_brevets` avec la query
2. Par défaut collection FR. Ajouter EP/WO si demandé
3. Présenter : titre, numéro, déposant, inventeurs, date de dépôt, classification CPC, statut
4. Pour le détail : `inpi_get_brevet(numero)`

**Astuces SolR** :
- Par déposant : `deposant_s:"Safran"`
- Par classification : `cpc_s:H01M`
- Par date : `dateDepot_dt:[2023-01-01T00:00:00Z TO *]`

### 6. Recherche de dessins et modèles

Même logique que marques/brevets :
1. `inpi_search_dessins` avec la query
2. Présenter : titre, numéro, titulaire, classification Locarno, dates, statut
3. Détail via `inpi_get_dessin(numero)`

## Bonnes pratiques

- **Toujours utiliser `response_format: "markdown"`** pour les outils qui le supportent — ça produit une sortie plus lisible.
- **Paralléliser les appels** quand ils sont indépendants (ex : `inpi_get_company` + `inpi_get_beneficiaires` + `inpi_get_attachments` en même temps).
- **Actes : lister, ne pas lire automatiquement.** Les sociétés anciennes peuvent avoir plus de 100 actes — les résumer par date suffit. Laisser l'utilisateur demander un acte spécifique.
- **Bilans : lire automatiquement si l'utilisateur a demandé des données financières.** C'est la différence clé avec les actes : quand quelqu'un dit "les comptes" ou "le bilan", il s'attend à voir les chiffres, pas juste une liste de fichiers. Aller chercher le bilan complet (type C) le plus récent.
- **Dates historiques** : `inpi_get_company` accepte un paramètre `date` pour voir l'état de la société à une date passée — utile pour vérifier qui était dirigeant à un moment donné.
- **Sortie texte** : cette skill produit du texte structuré dans le chat. Si l'utilisateur veut un document formel (Word, PDF, Excel), utiliser les skills dédiées (docx, pdf, xlsx) en complément.
- **Recherches SolR avancées** : les outils de recherche marques/brevets/dessins supportent la syntaxe SolR pour des filtres précis. Proposer cette option quand l'utilisateur a un besoin spécifique (recherche par déposant, par classe, par période).
- **Formatage des montants** : les données INPI sont en euros. Formater lisiblement les grands montants : utiliser "Md€" pour les milliards, "M€" pour les millions (ex : 2 435 000 000 → "2,4 Md€").
