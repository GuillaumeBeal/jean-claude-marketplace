---
name: verifier
description: "Vérifier une citation ou référence juridique : article de code, loi, décret, NOR, ECLI. Contrôle que le texte est exact, en vigueur, et non modifié."
---

# /verifier — Vérification de référence juridique

Vérifie qu'une citation juridique est exacte et à jour, via le MCP Légifrance.

## Types de références vérifiables

| Type | Exemple | Outil |
|------|---------|-------|
| Article de code | art. 1240 Code civil | `get_article(textId: "...", num: "...")` |
| Loi par numéro | loi n° 2016-1691 | `search_texts` fond=LODA_DATE, typeChamp=NUM |
| NOR | JUSC1732516D | `get_jorf_text(nor: "JUSC1732516D")` |
| ECLI | ECLI:FR:CCASS:2021:... | `search_texts` fond=JURI, typeChamp=ECLI |
| IDCC | 1486 | `get_convention_container(idcc: "1486")` |
| Numéro de pourvoi | 21-12.345 | `search_texts` fond=JURI, typeChamp=NUM_AFFAIRE |

## Workflow

1. **Identifier le type de référence** dans la demande
2. **Appeler l'outil adapté** (voir table ci-dessus)
3. **Comparer** le texte cité par l'utilisateur avec le texte officiel
4. **Vérifier l'état** : en vigueur, abrogé, modifié
5. **Rendre un verdict**

## Verdicts possibles

- **Exacte** : le texte cité correspond au texte officiel en vigueur
- **Approximative** : le sens est correct mais la formulation diffère
- **Erronée** : le texte cité ne correspond pas
- **Modifiée** : l'article existe mais a été modifié depuis (préciser la date de modification)
- **Abrogée** : l'article ou le texte n'est plus en vigueur (indiquer le remplaçant via concordance)
- **Introuvable** : la référence n'existe pas dans Légifrance

## En cas d'article abrogé ou renuméroté

1. `get_article_links(articleId: "<LEGIARTI>", linkType: "concordance")` pour trouver la correspondance
2. Présenter l'ancien et le nouvel article

## Réponse attendue

- Verdict clair (voir ci-dessus)
- Texte officiel actuel
- Si différent : ce qui a changé et quand
- Date de version consultée
- Identifiant Légifrance pour référence
- Citation au format professionnel
