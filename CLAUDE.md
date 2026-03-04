# LeDroit Marketplace

Marketplace de skills (competences) en droit francais pour Claude Code.

## Structure du projet

```
ledroit-marketplace/
├── .claude-plugin/
│   └── marketplace.json          # Configuration principale de la marketplace
├── plugins/
│   └── legifrance-plugin/        # Plugin Legifrance (droit francais)
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── .mcp.json                 # Config du serveur MCP Legifrance
│       └── skills/
│           ├── legifrance/           # /legifrance — skill principale
│           │   ├── SKILL.md
│           │   └── references/
│           │       ├── tools-and-codes.md
│           │       ├── search-parameters.md
│           │       ├── veille-juridique.md
│           │       └── advanced-patterns.md
│           ├── article/              # /article — article de code
│           │   └── SKILL.md
│           ├── loi/                  # /loi — recherche de loi
│           │   └── SKILL.md
│           ├── jurisprudence/        # /jurisprudence — decisions de justice
│           │   └── SKILL.md
│           ├── convention/           # /convention — conventions collectives
│           │   └── SKILL.md
│           ├── veille/               # /veille — veille juridique
│           │   └── SKILL.md
│           ├── jo/                   # /jo — Journal Officiel
│           │   └── SKILL.md
│           └── verifier/             # /verifier — verification de reference
│               └── SKILL.md
├── CLAUDE.md                     # Ce fichier
└── README.md
```

## Conventions

- Chaque plugin est un dossier dans `plugins/` avec sa propre structure `.claude-plugin/`
- Les skills sont definies dans `plugins/<plugin>/skills/<skill>/SKILL.md`
- La marketplace est enregistree dans `.claude-plugin/marketplace.json`
- Langue principale : francais

## MCP Servers disponibles

### Legifrance (DILA - API PISTE)

Serveur MCP connecte a l'API officielle Legifrance (DILA). Permet d'acceder a l'ensemble du droit francais :

**Fonds disponibles :**
- **JORF** : Journal Officiel de la Republique Francaise
- **LODA** : Lois et Decrets (textes consolides)
- **CODE** : Codes en vigueur (Code civil, Code penal, Code du travail, etc.)
- **JURI** : Jurisprudence judiciaire (Cour de cassation, cours d'appel)
- **CETAT** : Jurisprudence administrative (Conseil d'Etat, cours administratives)
- **CONSTIT** : Decisions du Conseil constitutionnel
- **KALI** : Conventions collectives
- **CNIL** : Deliberations de la CNIL
- **CIRC** : Circulaires
- **ACCO** : Accords d'entreprise

**Outils principaux :**
- `searchUsingPOST` : Recherche plein texte dans tous les fonds
- `displayCodeUsingPOST` : Consultation d'un code par identifiant et date
- `displayCodeTableOfContentsUsingPOST` : Table des matieres d'un code
- `displayLawDecreeUsingPOST` : Consultation d'une loi/decret (LODA)
- `displayJorfUsingPOST` : Consultation d'un texte du JORF
- `displayJuriUsingPOST` : Consultation d'une decision de justice
- `displayKaliContUsingPOST` : Consultation d'une convention collective
- `getArticleUsingPOST` : Recuperation d'un article par identifiant
- `crossSearchUsingPOST` : Suggestions multi-fonds

**Identifiants courants :**
- Code civil : `LEGITEXT000006070721`
- Code penal : `LEGITEXT000006070719`
- Code du travail : `LEGITEXT000006072050`
- Code de commerce : `LEGITEXT000005634379`
- Code de procedure civile : `LEGITEXT000006070716`
- Constitution de 1958 : `LEGITEXT000006071194`

### data.gouv.fr

Serveur MCP pour l'acces aux jeux de donnees publics francais (open data).

## Workflow type pour une recherche juridique

1. Utiliser `searchUsingPOST` pour trouver les textes pertinents
2. Utiliser `displayCodeUsingPOST` ou `displayLawDecreeUsingPOST` pour consulter le texte
3. Utiliser `getArticleUsingPOST` pour obtenir un article precis
4. Utiliser `displayJuriUsingPOST` pour la jurisprudence associee
