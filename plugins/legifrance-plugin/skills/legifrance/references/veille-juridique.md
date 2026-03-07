# Veille Juridique — Workflow et Mots-clés

## Workflow en 4 étapes

### Étape 1 — Cadrer la demande
Identifier : domaine juridique, période, niveau de détail souhaité.

### Étape 2 — Lancer les recherches parallèles sur 4-5 fonds

**a) Jurisprudence judiciaire (JURI)**
```
searchUsingPOST(fond: "JURI", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DECISION", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 20,
  sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```

**b) Jurisprudence administrative (CETAT)**
Même structure avec `fond: "CETAT"`.

**c) Textes législatifs (LODA)**
```
searchUsingPOST(fond: "LODA_DATE", recherche: {
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
```
searchUsingPOST(fond: "JORF", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_PUBLICATION", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "PUBLICATION_DATE_DESC", typePagination: "DEFAUT"
})
```
⚠ JORF : utiliser `DATE_PUBLICATION` (pas `DATE_PUBLI` → erreur 500).

**e) Délibérations CNIL** (si domaine RGPD / données personnelles)
```
searchUsingPOST(fond: "CNIL", recherche: {
  champs: [{typeChamp: "ALL", criteres: [{
    typeRecherche: "TOUS_LES_MOTS_DANS_UN_CHAMP",
    valeur: "<mots-clés du domaine>", operateur: "ET"
  }], operateur: "ET"}],
  filtres: [{facette: "DATE_DELIB", dates: {start: "<début>", end: "<fin>"}}],
  operateur: "ET", pageNumber: 1, pageSize: 10,
  sort: "DATE_DECISION_DESC", typePagination: "DEFAUT"
})
```
⚠ CNIL : utiliser `DATE_DELIB` (pas `DATE_DECISION` → erreur 500).

### Étape 3 — Approfondir les résultats pertinents
- Jurisprudence : `displayJuriUsingPOST(textId: "JURITEXT...")`
- Loi/décret : `displayLawDecreeUsingPOST(textId: "LEGITEXT...", date: "...")`
- JORF : `displayJorfUsingPOST(textCid: "JORFTEXT...")`
- CNIL : `displayCnilUsingPOST(textId: "CNILTEXT...")`

### Étape 4 — Structurer la revue

```
## Revue d'actualité — [Domaine] — [Période]

### 📜 Textes législatifs et réglementaires
- [Loi/Décret n°... du ...] — [Résumé]

### ⚖️ Jurisprudence marquante
- [Cass. com., date, n° pourvoi] — [Solution]
- [CE, date, n° affaire] — [Solution]

### 📰 Publications au Journal Officiel
- [Texte] — [Résumé]

### 💡 Points d'attention
- [Changements à surveiller, entrées en vigueur prochaines]
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
- Fonds prioritaires : CETAT, JURI, CODE_DATE (CGI)

### Droit immobilier
- Construction : `"construction maître ouvrage permis"`
- Bail : `"bail commercial locataire bailleur"`
- Copropriété : `"copropriété syndic assemblée"`
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

---

## Stratégies par durée

### Courte (1 semaine)
- `getLastNJoUsingPOST(nbElement: 7)` pour les JO récents
- Recherches avec pageSize: 10
- Focus sur les textes publiés et décisions rendues

### Moyenne (1-3 mois)
- pageSize: 20
- Filtrer JURI par `COUR_CASSATION` pour ne garder que les décisions significatives
- Ajouter filtre `CASSATION_TYPE_PUBLICATION_BULLETIN: ["T"]` pour les arrêts publiés

### Longue (6-12 mois)
- Décomposer en trimestres pour ne pas dépasser la pagination
- Focus sur les décisions publiées au Bulletin
- Synthétiser par tendances et évolutions
