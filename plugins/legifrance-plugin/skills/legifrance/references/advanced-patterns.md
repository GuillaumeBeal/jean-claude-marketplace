# Patterns avancés de résolution juridique

Patterns détaillés pour les cas complexes. Consulter quand les workflows de base du SKILL.md ne suffisent pas.

## Pattern 1 : Résolution de référence ambiguë

L'utilisateur mentionne "l'article 1382" devenu "l'article 1240" après la réforme de 2016.

1. `getArticleWithIdAndNumUsingPOST(id: "LEGITEXT000006070721", num: "1382")`
2. Si `etat: "ABROGE"` ou `"TRANSFERE"` → `displayConcordanceLinksArticleUsingPOST(articleId: "<LEGIARTI>")`
3. Expliquer la correspondance ancien → nouveau

Tables de concordance fréquentes :
- Code civil art. 1101-1386 → renumérotés en 2016 (ordonnance n° 2016-131)
- Code du travail ancien → nouveau (recodification 2008)
- Code de commerce ancien → nouveau (2000)

## Pattern 2 : Recherche multi-fonds

Pour une question transversale ("obligations RGPD des entreprises ?") :

1. `searchUsingPOST(fond: "CODE_DATE", ...)` avec mots-clés thématiques
2. Même recherche avec `fond: "LODA_DATE"`
3. Même recherche avec `fond: "CNIL"`
4. Synthèse des résultats des 3 fonds

## Pattern 3 : Traçabilité d'une modification

1. `getArticleWithIdAndNumUsingPOST` → LEGIARTI actuel
2. `searchCanonicalArticleVersionUsingPOST(id: "<LEGIARTI>")` → historique complet
3. Comparer les `dateDebut` de chaque version
4. `getArticleUsingPOST(id: "<LEGIARTI version>")` pour le détail

## Pattern 4 : Jurisprudence ciblée

**Par n° de pourvoi :** `typeChamp: "NUM_AFFAIRE"`, `typeRecherche: "EXACTE"`, `valeur: "21-12.345"`

**Par ECLI :** `typeChamp: "ECLI"`, `typeRecherche: "EXACTE"`, `valeur: "ECLI:FR:CCASS:2021:..."`

**Thématique avec filtres :**
```
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP", valeur: "responsabilité contractuelle", operateur: "ET"}], operateur: "ET"}],
  filtres: [
    {facette: "DATE_DECISION", dates: {start: "2023-01-01", end: "2026-03-01"}},
    {facette: "JURIDICTION", valeurs: ["COUR_CASSATION"]}
  ],
  operateur: "ET", pageNumber: 1, pageSize: 10, sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```

## Pattern 5 : Navigation convention collective

1. `displayKaliContByIdccUsingPOST(id: "1486")` → structure
2. `displayKaliSectionUsingPOST(id: "KALISCTA...")` → section ciblée
3. `displayKaliArticleUsingPOST(id: "KALIARTI...")` → article
4. Recherche thématique dans une CC :
```
searchUsingPOST(fond: "KALI", filtres: [{facette: "IDCC", valeurs: ["1486"]}], ...)
```

## Pattern 6 : Droit applicable à une date donnée

1. `getArticleWithIdAndNumUsingPOST` pour l'article actuel → obtenir le CID
2. `getArticleByCidUsingPOST(cid: "<CID>")` → toutes les versions
3. Trouver la version dont `dateDebut ≤ date demandée < dateFin`

Ou directement : `displayCodeUsingPOST(textId: "...", date: "2012-06-15")`

## Pattern 7 : Vérification de citation

1. `getArticleWithIdAndNumUsingPOST` pour récupérer le texte exact
2. Comparer avec la citation de l'utilisateur
3. Verdict : ✅ exacte / ⚠️ approximative / ❌ erronée / 📅 modifiée / ⛔ abrogée

## Pattern 8 : Textes récents au JORF

1. `getLastNJoUsingPOST(nbElement: 7)` → derniers JO
2. `displayJorfContUsingPOST(id: "JORFCONT...")` → explorer un JO
3. `displayJorfUsingPOST(textCid: "JORFTEXT...")` → lire un texte

## Mapping des numérotations

| Préfixe | Signification |
|---------|---------------|
| L | Partie législative |
| R | Partie réglementaire (décrets en Conseil d'État) |
| D | Partie réglementaire (décrets simples) |
| A | Arrêtés |

Format : `num: "L225-1"` (sans point). Fallback : essayer `"225-1"` si le préfixe ne fonctionne pas.
