---
name: verifier
description: "Verifier une citation ou reference juridique : article de code, loi, decret, NOR, ECLI. Controle que le texte est exact, en vigueur, et non modifie."
---

# /verifier — Verification de reference juridique

Verifie qu'une citation juridique est exacte et a jour, via le MCP Legifrance.

## Types de references verifiables

| Type | Exemple | Outil |
|------|---------|-------|
| Article de code | art. 1240 Code civil | `getArticleWithIdAndNumUsingPOST` |
| Loi par numero | loi n° 2016-1691 | `searchUsingPOST` fond=LODA_DATE, typeChamp=NUM |
| NOR | JUSC1732516D | `getJoWithNorUsingPOST` |
| ECLI | ECLI:FR:CCASS:2021:... | `searchUsingPOST` fond=JURI, typeChamp=ECLI |
| IDCC | 1486 | `displayKaliContByIdccUsingPOST` |
| Numero de pourvoi | 21-12.345 | `searchUsingPOST` fond=JURI, typeChamp=NUM_AFFAIRE |

## Workflow

1. **Identifier le type de reference** dans la demande
2. **Appeler l'outil adapte** (voir table ci-dessus)
3. **Comparer** le texte cite par l'utilisateur avec le texte officiel
4. **Verifier l'etat** : en vigueur, abroge, modifie
5. **Rendre un verdict**

## Verdicts possibles

- **Exacte** : le texte cite correspond au texte officiel en vigueur
- **Approximative** : le sens est correct mais la formulation differe
- **Erronee** : le texte cite ne correspond pas
- **Modifiee** : l'article existe mais a ete modifie depuis (preciser la date de modification)
- **Abrogee** : l'article ou le texte n'est plus en vigueur (indiquer le remplacant via concordance)
- **Introuvable** : la reference n'existe pas dans Legifrance

## En cas d'article abroge ou renumerote

1. `displayConcordanceLinksArticleUsingPOST(articleId: "<LEGIARTI>")` pour trouver la correspondance
2. Presenter l'ancien et le nouvel article

Tables de concordance frequentes :
- Code civil art. 1101-1386 → renumerotes en 2016 (ordonnance n° 2016-131)
- Code du travail ancien → nouveau (recodification 2008)
- Code de commerce ancien → nouveau (2000)

## Reponse attendue

- Verdict clair (voir ci-dessus)
- Texte officiel actuel
- Si different : ce qui a change et quand
- Date de version consultee
- Identifiant Legifrance pour reference
