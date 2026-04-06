# Veille Juridique — Workflow et Mots-clés

## Workflow en 5 étapes

### Étape 1 — Cadrer la demande
Identifier : domaine juridique, période, niveau de détail souhaité, audience (avocat, juriste d'entreprise, direction juridique).

### Étape 2 — Explorer le graphe (si sujet conceptuel)

Avant de lancer les recherches textuelles, vérifier le graphe :
```
query_legal_graph(concept: "<concept du domaine>", limit: 10)
```
Si des articles/décisions/textes sont déjà indexés → les inclure directement dans la revue.
Sinon → passer à l'étape 3.

### Étape 3 — Lancer les recherches parallèles sur 4-5 fonds

**a) Jurisprudence judiciaire (JURI)**
```
search_texts(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 20,
  sort: "DATE_DESC", typePagination: "DEFAUT"
})
```

**b) Jurisprudence administrative (CETAT)**
Même structure avec `fond: "CETAT"`.

**c) Textes législatifs (LODA)**
```
search_texts(fond: "LODA_DATE", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_SIGNATURE", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "SIGNATURE_DATE_DESC", typePagination: "DEFAUT"
})
```

**d) Journal Officiel (JORF)**
Même structure avec `fond: "JORF"`, facette `DATE_PUBLICATION`, tri `PUBLICATION_DATE_DESC`.
⚠ JORF : utiliser `DATE_PUBLICATION` (pas `DATE_PUBLI` → erreur 500).

**e) Délibérations CNIL** (si domaine RGPD / données personnelles)
Même structure avec `fond: "CNIL"`, facette `DATE_DELIB`, tri `DATE_DECISION_DESC`.
⚠ CNIL : utiliser `DATE_DELIB` (pas `DATE_DECISION` → erreur 500).

**f) Conseil constitutionnel** (si QPC ou contrôle de constitutionnalité)
Même structure avec `fond: "CONSTIT"`, facette `DATE_DECISION`, filtre optionnel `NATURE_CONSTIT: ["QPC"]`.

### Étape 4 — Approfondir les résultats pertinents
- Jurisprudence : `get_jurisprudence(textId: "JURITEXT...")` + `get_jurisprudence_classification(textId: "...")` pour le plan de classement
- Loi/décret : `get_law_decree(textId: "LEGITEXT...", date: "...")` ou `get_jorf_text(textCid: "JORFTEXT...")`
- CNIL : `get_cnil_text(textId: "CNILTEXT...")`
- Dossier législatif : `get_dossier_legislatif(id: "JORFDOLE...")` pour le parcours complet d'une loi

### Étape 5 — Structurer la revue

```
## Revue d'actualité — [Domaine] — [Période]

### Textes législatifs et réglementaires
- [Loi/Décret n°... du ...] — [Résumé et impact pratique]

### Jurisprudence marquante
- [Cass. com., date, n° pourvoi, publié au Bull.] — [Solution et portée]
- [CE, date, n° affaire] — [Solution et portée]

### Décisions CNIL / QPC (le cas échéant)
- [Référence] — [Résumé]

### Publications au Journal Officiel
- [Texte] — [Résumé]

### Points d'attention
- [Changements à surveiller, entrées en vigueur prochaines, réformes annoncées]
```

---

## Mots-clés par domaine juridique

### Droit des sociétés
- Général : `"société commerciale gouvernance assemblée"`
- Dirigeants : `"conseil administration dirigeant révocation responsabilité"`
- M&A : `"fusion acquisition cession"`
- Capital : `"augmentation capital apport émission actions"`
- Fonds prioritaires : JURI (Chambre commerciale), LODA, CODE_DATE (Code de commerce)

### Droit du travail
- Contrat : `"contrat travail salarié"`
- Rupture : `"licenciement rupture motif"`
- Harcèlement : `"harcèlement moral sexuel discrimination"`
- Collectif : `"comité social économique négociation collective"`
- Fonds prioritaires : JURI (Chambre sociale), KALI, CODE_DATE (Code du travail)

### Droit fiscal
- Général : `"impôt fiscal contribuable"`
- TVA : `"TVA déduction taxe valeur ajoutée"`
- Contrôle : `"redressement vérification comptabilité"`
- Prix de transfert : `"prix transfert entreprise liée"`
- Fonds prioritaires : CETAT, JURI, CODE_DATE (CGI)

### Droit immobilier
- Construction : `"construction maître ouvrage permis"`
- Bail : `"bail commercial locataire bailleur"`
- Copropriété : `"copropriété syndic assemblée"`
- Urbanisme : `"urbanisme permis construire PLU"`
- Fonds prioritaires : JURI (3e chambre civile), CETAT, CODE_DATE (CCH, urbanisme)

### ESG / CSRD
- Reporting : `"durabilité reporting extra-financier"`
- CSRD : `"CSRD information"`
- Climat : `"climat émissions carbone"`
- Vigilance : `"vigilance devoir plan"`
- Fonds prioritaires : LODA, JORF, CODE_DATE (Code de commerce, Code environnement)

### Droit pénal des affaires
- Général : `"abus biens sociaux détournement"`
- Fraude : `"fraude fiscale blanchiment"`
- Corruption : `"corruption trafic influence"`
- Fonds prioritaires : JURI (Chambre criminelle), LODA

### Droit du numérique / RGPD
- Données : `"données personnelles traitement responsable"`
- IA : `"intelligence artificielle algorithme"`
- Cyber : `"cybersécurité sécurité informatique"`
- Fonds prioritaires : CNIL, CETAT, JURI, LODA

### Propriété intellectuelle
- Marques : `"marque contrefaçon distinctif"`
- Brevets : `"brevet invention contrefaçon"`
- Droit d'auteur : `"auteur reproduction droit oeuvre"`
- Fonds prioritaires : JURI, CODE_DATE (CPI)

### Droit bancaire et financier
- Général : `"bancaire crédit prêt emprunteur"`
- Régulation : `"AMF autorité marchés financiers"`
- Financement : `"garantie sûreté cautionnement nantissement"`
- Fonds prioritaires : JURI (Chambre commerciale), CETAT, CODE_DATE (CMF)

### Droit de la concurrence
- Pratiques anticoncurrentielles : `"entente concurrence pratique anticoncurrentielle"`
- Abus de position dominante : `"abus position dominante marché"`
- Concentrations : `"concentration opération notification"`
- Fonds prioritaires : CETAT, JURI (Chambre commerciale), LODA

---

## Stratégies par durée

### Courte (1 semaine)
- `get_last_journals(nbElement: 3)` pour les JO récents (limiter à 3 pour le contexte)
- Recherches avec pageSize: 10
- Focus sur les textes publiés et décisions rendues

### Moyenne (1-3 mois)
- pageSize: 20
- Filtrer JURI par `COUR_CASSATION` pour ne garder que les décisions significatives
- Ajouter filtre `CASSATION_TYPE_PUBLICATION_BULLETIN: ["T"]` pour les arrêts publiés
- Inclure CONSTIT si QPC pertinentes

### Longue (6-12 mois)
- Décomposer en trimestres pour ne pas dépasser la pagination
- Focus sur les décisions publiées au Bulletin
- Synthétiser par tendances et évolutions
- Inclure dossiers législatifs pour les réformes en cours
