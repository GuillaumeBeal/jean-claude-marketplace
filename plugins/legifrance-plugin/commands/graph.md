---
name: graph
description: "Explorer le graphe de connaissances juridiques : concepts, domaines, articles liés. Recherche sémantique avant textuelle. Utile pour cartographier un sujet juridique."
---

# /graph — Exploration du graphe de connaissances

Recherche sémantique dans le graphe de connaissances juridiques avant de lancer des recherches textuelles.

## Workflow

### Recherche par concept
```
query_legal_graph(concept: "responsabilité civile", limit: 20)
```
Retourne : articles, décisions, textes liés au concept.

### Explorer les concepts voisins
```
get_concept_network(concept: "clause résolutoire", depth: 2, limit: 30)
```
Retourne : concepts voisins, domaines associés, connexions inter-domaines.

### Vue d'ensemble des domaines
```
list_graph_domains()
```
Sans paramètre : liste tous les domaines avec statistiques.
Avec paramètre : détail d'un domaine spécifique.
```
list_graph_domains(domaine: "droit_civil")
```

### Contexte d'un article
```
get_article_context(article_id: "LEGIARTI000032041571")
```
Retourne : concepts associés, décisions qui citent l'article, domaine juridique.

## Domaines actuellement indexés

Le graphe contient ~567 concepts et ~779 articles. Domaines disponibles :
- `droit_administratif` (213 concepts)
- `droit_social` (159 concepts)
- `droit_commercial` (175 concepts)
- `droit_civil` (24 concepts)
- `droit_construction`, `droit_fiscal`, `droit_immobilier` (en cours d'enrichissement)

## Réponse attendue

- Concepts identifiés et leurs connexions
- Articles et décisions liés (avec IDs pour accès direct)
- Domaines juridiques concernés
- Suggestion de recherches complémentaires si le graphe est incomplet
