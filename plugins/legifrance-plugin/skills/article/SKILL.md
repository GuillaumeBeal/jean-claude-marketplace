---
name: article
description: "Consulter un article de code francais. Accepte : 'article 1240 du code civil', 'L225-1 code de commerce', 'R4121-1 code du travail'. Resout automatiquement le code et le numero."
---

# /article — Consultation d'un article de code

Acces direct a un article d'un code francais via le MCP Legifrance.

## Workflow

1. **Identifier le code** a partir de la demande de l'utilisateur
2. **Appeler `getArticleWithIdAndNumUsingPOST`** avec le LEGITEXT du code et le numero d'article
3. **Presenter** le contenu, l'etat (VIGUEUR/ABROGE) et la date de version
4. **Si abroge** : utiliser `displayConcordanceLinksArticleUsingPOST` pour trouver le nouvel article

## Identifiants des codes

| Code | LEGITEXT |
|------|----------|
| Code civil | `LEGITEXT000006070721` |
| Code penal | `LEGITEXT000006070719` |
| Code du travail | `LEGITEXT000006072050` |
| Code de commerce | `LEGITEXT000005634379` |
| Code de procedure civile | `LEGITEXT000006070716` |
| Code de procedure penale | `LEGITEXT000006071154` |
| Code de la securite sociale | `LEGITEXT000006073189` |
| Code general des impots (CGI) | `LEGITEXT000006069577` |
| Code de l'environnement | `LEGITEXT000006074220` |
| Code de la sante publique | `LEGITEXT000006072665` |
| Code de la propriete intellectuelle | `LEGITEXT000006069414` |
| Code monetaire et financier | `LEGITEXT000006072026` |
| Code de l'urbanisme | `LEGITEXT000006074075` |
| Code de la construction et de l'habitation | `LEGITEXT000006074096` |
| Code de la consommation | `LEGITEXT000006069565` |
| Code general des collectivites territoriales | `LEGITEXT000006070633` |
| Constitution du 4 octobre 1958 | `LEGITEXT000006071194` |
| Livre des procedures fiscales | `LEGITEXT000006069583` |
| Code de l'energie | `LEGITEXT000023983208` |

## Format du numero d'article

SANS point : `"1240"`, `"L225-1"`, `"R123-4"`, `"D456-7"`, `"L1234-5"`

## Fallback si non trouve

1. Essayer sans prefixe (`"225-1"` au lieu de `"L225-1"`)
2. Si toujours rien : `searchUsingPOST` avec `typeChamp: "NUM_ARTICLE"` dans le fond `CODE_DATE`

## Reponse attendue

- Texte complet de l'article
- Etat : en vigueur / abroge / modifie
- Date de version consultee
- Position dans le code (section, chapitre)
- Si abroge : indiquer le nouvel article correspondant via concordance
- Source : identifiant LEGIARTI et lien implicite Legifrance
