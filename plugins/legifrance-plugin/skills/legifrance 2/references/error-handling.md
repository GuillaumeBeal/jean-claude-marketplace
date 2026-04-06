# Gestion d'erreurs et fallbacks

## Erreurs 500 — Prévention

Les erreurs 500 les plus fréquentes viennent de **mauvaises facettes de date**. Avant de lancer un `search_texts`, toujours vérifier dans la table du SKILL.md.

| Erreur | Cause | Correction |
|--------|-------|------------|
| 500 sur JORF | `DATE_PUBLI` au lieu de `DATE_PUBLICATION` | Utiliser `DATE_PUBLICATION` |
| 500 sur CNIL | `DATE_DECISION` au lieu de `DATE_DELIB` | Utiliser `DATE_DELIB` |
| 500 sur KALI/ACCO | `DATE_DECISION` | Utiliser `DATE_SIGNATURE` |
| 500 sur CODE_DATE | `dates` range avec `DATE_VERSION` | Utiliser `singleDate` uniquement |
| 500 générique | Paramètre `sort` invalide pour le fond | Vérifier `references/search-parameters.md` |

## Article non trouvé

**Symptôme** : `get_article(textId, num)` retourne une erreur ou un résultat vide.

**Fallback en 4 étapes** :
1. **Retirer le point** : `"L.225-1"` → `"L225-1"`
2. **Retirer le préfixe** : `"L225-1"` → `"225-1"`
3. **Recherche par numéro** :
   ```json
   search_texts(fond: "CODE_DATE", recherche: {
     champs: [{typeChamp: "NUM_ARTICLE", criteres: [{
       typeRecherche: "EXACTE", valeur: "225-1", operateur: "ET"
     }], operateur: "ET"}],
     operateur: "ET", pageNumber: 1, pageSize: 5,
     sort: "PERTINENCE", typePagination: "DEFAUT"
   })
   ```
4. **Vérifier le code** : l'utilisateur a peut-être le mauvais code (ex: article dans le Code de commerce, pas le Code civil)

**Message utilisateur** :
> « L'article [X] n'a pas été trouvé dans [code]. Il peut avoir été renuméroté ou abrogé. Voulez-vous que je recherche par concordance ou dans un autre code ? »

## Recherche sans résultat

**Symptôme** : `search_texts` retourne `totalResultNumber: 0`.

**Fallback en 3 étapes** :
1. **Élargir le type de recherche** : passer de `TOUS_LES_MOTS_DANS_UN_CHAMP` à `UN_DES_MOTS`
2. **Élargir la période** : doubler la plage de dates
3. **Changer de fond** : si JURI ne donne rien, essayer CETAT ou inversement
4. **Simplifier les mots-clés** : réduire à 2-3 mots essentiels

**Message utilisateur** :
> « La recherche sur [fond] avec les termes "[mots-clés]" n'a pas retourné de résultat sur la période [dates]. Je peux élargir la recherche (termes plus larges, période étendue) ou chercher dans un autre fonds. »

## Article abrogé

**Symptôme** : `etat: "ABROGE"` dans la réponse.

**Action** :
1. `get_article_links(articleId: "<LEGIARTI>", linkType: "concordance")` pour trouver le nouvel article
2. Si concordance trouvée → récupérer le nouvel article via `get_article`
3. Présenter l'ancien ET le nouveau à l'utilisateur

**Message utilisateur** :
> « L'article [X] a été abrogé le [date]. Il a été remplacé par l'article [Y] du même code (concordance : [ancien] → [nouveau]). Voici le texte actuellement en vigueur : [...] »

## Convention collective introuvable par IDCC

**Fallback** :
1. `list_conventions(titre: "<nom approximatif>")` pour chercher par titre
2. Si trouvé → utiliser l'IDCC retourné
3. Si pas trouvé → `search_texts(fond: "KALI", ...)` avec mots-clés thématiques

## Réponse MCP trop volumineuse (contexte saturé)

**Symptôme** : Réponse tronquée ou timeout.

**Prévention** :
- `get_last_journals` : limiter à `nbElement: 3`
- `get_table_of_contents` : **ne jamais utiliser sur les gros codes**. Utiliser `get_filtered_table_of_contents` avec `max_depth: 2` et un `section_filter`
- `get_code_content` : **toujours fournir `sctCid`** pour cibler une section
- `search_texts` : commencer avec `pageSize: 5`, augmenter si besoin

## Graphe de connaissances vide

**Symptôme** : `query_legal_graph` retourne des listes vides.

**Contexte** : Le graphe est en cours d'enrichissement. Certains domaines (fiscal, immobilier, construction) ont peu ou pas de données.

**Fallback** : Passer directement à `search_texts`. Ne pas mentionner le graphe à l'utilisateur si vide.

## Erreur de connexion / timeout

**Action** :
1. Retenter une fois
2. Si persistant → informer l'utilisateur
3. Proposer de reformuler la requête (requête plus simple = moins de charge)

**Message utilisateur** :
> « Le service Légifrance est temporairement indisponible. Je peux retenter dans un instant ou reformuler la recherche. »

## Résultat ambigu (plusieurs textes possibles)

**Action** :
1. Présenter les 2-3 résultats les plus pertinents avec titre, date, nature
2. Demander à l'utilisateur de préciser
3. Ne jamais deviner — en droit, la précision est critique

**Message utilisateur** :
> « Plusieurs textes correspondent à votre recherche : [liste]. Lequel souhaitez-vous consulter ? »
