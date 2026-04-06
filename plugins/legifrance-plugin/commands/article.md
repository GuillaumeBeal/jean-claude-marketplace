---
name: article
description: "Consulter un article de code français. Accepte : 'article 1240 du code civil', 'L225-1 code de commerce', 'R4121-1 code du travail'. Résout automatiquement le code et le numéro."
---

# /article — Consultation d'un article de code

Accès direct à un article d'un code français via le MCP Légifrance.

## Workflow

1. **Identifier le code** à partir de la demande de l'utilisateur
2. **Appeler `get_article`** avec le LEGITEXT du code et le numéro d'article :
   ```
   get_article(textId: "<LEGITEXT>", num: "<numéro>")
   ```
3. **Présenter** le contenu, l'état (VIGUEUR/ABROGE) et la date de version
4. **Si abrogé** : `get_article_links(articleId: "<LEGIARTI>", linkType: "concordance")` pour trouver le nouvel article
5. **Enrichissement optionnel** : `get_article_context(article_id: "<LEGIARTI>")` pour les concepts associés et décisions citantes

## Identifiants des codes

| Code | LEGITEXT |
|------|----------|
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
| Code de la consommation | `LEGITEXT000006069565` |
| Code général des collectivités territoriales | `LEGITEXT000006070633` |
| Constitution du 4 octobre 1958 | `LEGITEXT000006071194` |
| Livre des procédures fiscales | `LEGITEXT000006069583` |
| Code de l'énergie | `LEGITEXT000023983208` |

## Format du numéro d'article

SANS point : `"1240"`, `"L225-1"`, `"R123-4"`, `"D456-7"`, `"L1234-5"`

## Fallback si non trouvé

1. Essayer sans préfixe (`"225-1"` au lieu de `"L225-1"`)
2. Si toujours rien : `search_texts` avec `typeChamp: "NUM_ARTICLE"` dans le fond `CODE_DATE`

## Réponse attendue

- Texte complet de l'article
- État : en vigueur / abrogé / modifié
- Date de version consultée
- Position dans le code (section, chapitre)
- Si abrogé : indiquer le nouvel article correspondant via concordance
- Citation au format professionnel : `Art. [num] du [Code] (LEGIARTI[...])`
