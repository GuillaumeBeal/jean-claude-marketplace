# Patterns avancés de résolution juridique

Patterns détaillés pour les cas complexes. Consulter quand les workflows de base du SKILL.md ne suffisent pas.

## Pattern 1 : Résolution de référence ambiguë (concordance)

L'utilisateur mentionne "l'article 1382" devenu "l'article 1240" après la réforme de 2016.

1. `get_article(textId: "LEGITEXT000006070721", num: "1382")`
2. Si `etat: "ABROGE"` ou `"TRANSFERE"` → consulter le champ `lienConcordes` dans la réponse
3. `lienConcordes` contient la correspondance ancien → nouveau (LEGIARTI de l'article remplaçant)
4. `get_article(id: "<nouveau LEGIARTI>")` → texte actuel
5. Expliquer la correspondance ancien → nouveau à l'utilisateur

Tables de concordance fréquentes :
- Code civil art. 1101-1386 → renumérotés en 2016 (ordonnance n° 2016-131)
- Code du travail ancien → nouveau (recodification 2008)
- Code de commerce ancien → nouveau (2000)

## Pattern 2 : Recherche multi-fonds (question transversale)

Pour une question transversale ("obligations RGPD des entreprises ?") :

1. `query_legal_graph(concept: "RGPD obligations entreprise")` → check graphe d'abord
2. `search_texts(fond: "CODE_DATE", ...)` avec mots-clés thématiques
3. `search_texts(fond: "LODA_DATE", ...)` même recherche
4. `search_texts(fond: "CNIL", ...)` même recherche
5. Synthèse croisée des résultats

## Pattern 3 : Traçabilité d'une modification (historique d'un article)

1. `get_article(textId: "...", num: "...")` → LEGIARTI actuel + CID
2. `search_nearest_version(cidText: "<LEGITEXT>", date: "2025-01-01", mode: "article_versions", articleId: "<LEGIARTI>")` → historique complet
3. Comparer les `dateDebut` de chaque version
4. `get_article(id: "<LEGIARTI version spécifique>")` pour le détail d'une version

Alternative :
1. `get_article(cid: "<CID>")` → toutes les versions d'un coup
2. Analyser le tableau `articleVersions` retourné

## Pattern 4 : Jurisprudence ciblée

**Par n° de pourvoi :**
```json
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "NUM_AFFAIRE", criteres: [{
    typeRecherche: "EXACTE", valeur: "21-12.345", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 5,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

**Par ECLI :**
```json
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ECLI", criteres: [{
    typeRecherche: "EXACTE", valeur: "ECLI:FR:CCASS:2021:...", operateur: "ET"
  }], operateur: "ET"}],
  operateur: "ET", pageNumber: 1, pageSize: 5,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

**Thématique avec filtres (arrêts publiés, chambre spécifique) :**
```json
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "responsabilité contractuelle",
    operateur: "ET"
  }], operateur: "ET"}],
  filtres: [
    {facette: "DATE_DECISION", dates: {start: "2023-01-01", end: "2026-04-06"}},
    {facette: "CASSATION_FORMATION", valeurs: ["CHAMBRE_COMMERCIALE"]},
    {facette: "CASSATION_TYPE_PUBLICATION_BULLETIN", valeurs: ["T"]}
  ],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "DATE_DESC", typePagination: "DEFAUT"
})
```

**Enrichissement :**
- `get_jurisprudence(textId: "JURITEXT...")` → texte complet
- `get_jurisprudence_classification(textId: "JURITEXT...")` → plan de classement (matières, mots-clés)
- `get_article_context(article_id: "<LEGIARTI cité>")` → voir quelles autres décisions citent le même article

## Pattern 5 : Navigation convention collective

1. `get_convention_container(idcc: "1486")` → structure du conteneur
2. Identifier la section pertinente dans la structure
3. `get_convention_text(id: "KALISCTA...", lookupBy: "section")` → section ciblée
4. `get_convention_text(id: "KALIARTI...", lookupBy: "article")` → article précis

Pour une recherche thématique dans une CC :
```json
search_texts(fond: "KALI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "période essai", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "IDCC", valeurs: ["1486"]}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "PERTINENCE", typePagination: "DEFAUT"
})
```

## Pattern 6 : Droit applicable à une date donnée

1. `get_article(textId: "...", num: "...")` → article actuel, obtenir le CID
2. `get_article(cid: "<CID>")` → toutes les versions
3. Trouver la version dont `dateDebut ≤ date demandée < dateFin`

Ou directement :
```
get_code_content(textId: "LEGITEXT000006070721", date: "2012-06-15", sctCid: "LEGISCTA...")
```

Ou via la version du texte :
```
search_nearest_version(cidText: "LEGITEXT000006070721", date: "2012-06-15")
```

## Pattern 7 : Vérification de citation

1. `get_article(textId: "...", num: "...")` pour récupérer le texte exact
2. Comparer avec la citation de l'utilisateur
3. Verdict : ✅ exacte / ⚠️ approximative / ❌ erronée / 📅 modifiée / ⛔ abrogée

Si abrogé :
1. Consulter `lienConcordes` dans la réponse de `get_article` → correspondance vers le nouvel article
2. `get_article(id: "<nouveau LEGIARTI>")` → récupérer le remplaçant
3. Présenter l'ancien ET le nouveau

## Pattern 8 : Textes récents au JORF

1. `get_last_journals(nbElement: 3)` → derniers JO (**limiter à 3 max**)
2. `get_jorf_summary(id: "JORFCONT...")` → explorer un JO spécifique
3. `get_jorf_text(textCid: "JORFTEXT...")` → lire un texte

## Pattern 9 : Graph-first (recherche exploratoire)

Quand la question est conceptuelle et non une référence précise :

1. `query_legal_graph(concept: "<concept>")` → articles, décisions, textes connus
2. Si résultats → accès direct aux IDs retournés
3. `get_concept_network(concept: "<concept>", depth: 2)` → concepts voisins pour élargir
4. `list_graph_domains(domaine: "<domaine>")` → vue d'ensemble du domaine
5. Si graphe vide → `search_texts` en fallback

## Pattern 10 : Parcours législatif complet

Pour comprendre l'historique et les travaux préparatoires d'une loi :

1. `search_texts(fond: "LODA_DATE", ...)` pour trouver la loi
2. `get_law_decree(textId: "...", date: "...")` → contenu
3. `list_texts(category: "dossiers_legislatifs", extra_params: {"legislatureId": 17, "type": "LOI_PUBLIEE"})` → trouver le dossier
4. `get_dossier_legislatif(id: "JORFDOLE...")` → parcours complet (projet/proposition, commissions, navette, promulgation)
5. `get_debate(id: "...")` → débats parlementaires si disponibles

## Mapping des numérotations d'articles

| Préfixe | Signification |
|---------|---------------|
| L | Partie législative |
| R | Partie réglementaire (décrets en Conseil d'État) |
| D | Partie réglementaire (décrets simples) |
| A | Arrêtés |

Format : `num: "L225-1"` (sans point). Fallback : essayer `"225-1"` si le préfixe ne fonctionne pas.
