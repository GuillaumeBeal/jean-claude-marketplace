# LeDroit Marketplace

Marketplace de skills en droit francais pour Claude Code.

Ce projet regroupe des plugins et competences permettant a Claude Code d'assister efficacement sur des problematiques de droit francais, en s'appuyant sur les sources officielles via l'API Legifrance.

## Skills disponibles

### /legifrance
Recherche et consultation du droit francais via l'API Legifrance (codes, lois, jurisprudence, conventions collectives, JORF).

La skill fonctionne avec le MCP Legifrance : elle fournit les workflows et patterns de recherche, le MCP fournit l'acces aux donnees. Inclut 4 fichiers de reference pour les cas avances (parametres de recherche, veille juridique, patterns de concordance, catalogue des 35+ outils).

## MCP Servers

### Legifrance (DILA - API PISTE)

Connecteur vers l'API officielle Legifrance, donnant acces a l'integralite du droit francais publie :

| Fonds | Description | Outil principal |
|-------|-------------|-----------------|
| **JORF** | Journal Officiel | `displayJorfUsingPOST` |
| **CODE** | Codes en vigueur | `displayCodeUsingPOST` |
| **LODA** | Lois et decrets consolides | `displayLawDecreeUsingPOST` |
| **JURI** | Jurisprudence judiciaire | `displayJuriUsingPOST` |
| **CETAT** | Jurisprudence administrative | `displayJuriUsingPOST` |
| **CONSTIT** | Conseil constitutionnel | `displayJuriUsingPOST` |
| **KALI** | Conventions collectives | `displayKaliContUsingPOST` |
| **CNIL** | Deliberations CNIL | `displayCnilUsingPOST` |
| **CIRC** | Circulaires | `displayCirculaireUsingPOST` |
| **ACCO** | Accords d'entreprise | `displayAccoUsingPOST` |

**Fonctionnalites :**
- Recherche plein texte multi-fonds (`searchUsingPOST`)
- Consultation de codes a une date donnee (versions consolidees)
- Navigation dans les tables des matieres
- Recuperation d'articles individuels avec historique des versions
- Acces aux conventions collectives par IDCC
- Consultation des dossiers legislatifs
- Acces au Journal Officiel par date ou numero

### data.gouv.fr

Connecteur vers la plateforme open data du gouvernement francais. Permet de rechercher et interroger les jeux de donnees publics.

## Structure du projet

```
ledroit-marketplace/
├── .claude-plugin/
│   └── marketplace.json        # Registre de la marketplace
├── plugins/
│   └── legifrance-plugin/      # Plugin Legifrance (droit francais)
│       ├── .claude-plugin/plugin.json
│       └── skills/legifrance/
│           ├── SKILL.md        # Skill /legifrance (prompt + workflows)
│           └── references/     # Documentation de reference
│               ├── tools-and-codes.md
│               ├── search-parameters.md
│               ├── veille-juridique.md
│               └── advanced-patterns.md
├── CLAUDE.md                   # Instructions pour Claude Code
└── README.md                   # Ce fichier
```

## Configuration MCP

Pour utiliser le serveur Legifrance, ajoutez-le a votre configuration MCP Claude Code. Le serveur se connecte a l'API PISTE de la DILA (Direction de l'information legale et administrative).

**Exemple de configuration (`~/.claude/settings.json` ou projet) :**

```json
{
  "mcpServers": {
    "legifrance": {
      "url": "https://<votre-instance-legifrance-mcp>"
    }
  }
}
```

> L'API Legifrance necessite des identifiants PISTE (client_id / client_secret) obtenus sur [piste.gouv.fr](https://piste.gouv.fr).

## Contribuer

Pour ajouter une nouvelle skill :

1. Creer un dossier dans `plugins/<nom-plugin>/`
2. Ajouter un `plugin.json` dans `.claude-plugin/`
3. Creer la skill dans `skills/<nom-skill>/SKILL.md`
4. Enregistrer le plugin dans `.claude-plugin/marketplace.json`

## Licence

Projet open source. Les donnees juridiques sont issues de sources publiques (Legifrance / DILA).
