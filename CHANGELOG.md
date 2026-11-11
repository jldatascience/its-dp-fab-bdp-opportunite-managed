# Changelog

Toutes les évolutions notables du dépôt `its-dp-fab-bdp-opportunite-managed` (configuration de déploiement,
pipelines, outillage, documentation) sont consignées ici, de la plus récente à la plus ancienne.

Format : [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/). Ce dépôt n'est pas versionné
par tag : chaque entrée est datée (`## AAAA-MM-JJ`). Les évolutions du socle commun sont décrites
dans le `CHANGELOG.md` de `its-dp-fab-cicd-common`, versionné par tag `vX.Y.Z`.

## 2026-11-11

### Added

- Initialisation du dépôt BDP Opportunite sur le modèle des consommateurs (socle
  `its-dp-fab-cicd-common`, tag `v1.5.0`, catégorie `bdp` ; modèle : dépôt BDP Compte, commit
  `d84403f`, et dépôts BDP Campaign, BDP Contact et BDP Lead, initialisés le même jour) :
  - `CICD/deployment/config.yml` : workspaces `FR - DP - <ENV> - FAB - BDP - Opportunite - MANAGED`
    pour DEV, ITG, PPR et PRD (confirmés le 11/11/2026, tiret simple),
    `repository_directory: "../../fabric"`, `item_types_in_scope` vide, clé `core.parameter` absente.
  - `CICD/deployment/dependencies.yml` : `role: consumer`, `category: bdp`,
    `framework.required: true` (workspaces Framework, `items: []`, `ado_gate`) ;
    `guid_policy.forbidden_prefixes` : workspaces BDP Opportunite DEV, ITG, PPR, PRD et Framework DEV,
    absents du registre du socle v1.5.0.
  - `CICD/deployment/parameter.yml` : aucune règle (en-tête : les deux familles admises).
  - `CICD/pipelines/CD.yml` : `repository: its-dp-fab-bdp-opportunite-managed`, `role: consumer`,
    porte Framework `ado`, coffre `frfrkvdevdp0001`, 3 service connections ARM distinctes ;
    source CI `its-dp-fab-opportunite-managed-CI`, reprise telle quelle de l'ancienne CD (sans
    « bdp- »). Remplace l'ancienne CD (ITG → PPR → PRD enchaînés sans approbation,
    `Fabric-CD-Secrets`, fabric-cicd 1.2.0, `deployment/deploy.py` avec ID en dur).
  - `CICD/pipelines/CI.yml` : template KPMG conservé ; déclencheurs `develop`, `release/*`, `main`
    (au lieu de `feature/its/411581_industrialisation_chaine_cicd`) ; SonarQube
    `Discovery_DP-FAB-OPPORTUNITE-MANAGED`, repris tel quel de l'ancienne CI ; filtre `"!/*.json"`
    entre guillemets (l'ancien `!**/*.json` non quoté était une étiquette YAML).
  - `CICD/pipelines/PR-Validation.yml` : template de validation du socle, sans paramètre.
  - `CICD/ref_dynamique/01_inventaire_github.py` à `04_recoupement.py` et `templates/` : outillage
    des consommateurs BDP (références Framework légitimes, sorties hors du dépôt) ; `01` classe
    aussi les GUID avec `guid_policy` de `dependencies.yml`.
  - `.gitattributes` et `.gitignore` : identiques aux dépôts MDS, Alexandrie, UCS et BDP.
  - README au format des autres consommateurs.
- `fabric/.gitkeep` : dossier des items suivi par Git avant la première synchronisation.

### Vérifié

- `yamllint` (règles du socle) sur `CICD/deployment` et `CICD/pipelines` : sans erreur.
- `python -m compileall CICD/ref_dynamique` : sans erreur.
- `deploy.py validate` (socle `v1.5.0`) : code 4 attendu (« Aucun item Fabric »). Sur une copie de
  travail munie d'un item fictif : 0 erreur, `repository_directory` résolu vers `fabric/`, matrice
  `bdp` acceptée ; un GUID BDP Opportunite DEV en dur est refusé (`GUID_HARDCODED`).
- Scripts 01, 03 et 04 exécutés sur le dépôt (vide) : aucune écriture dans le dépôt.

### À confirmer

- Workspace DEV comme source des items ; Git Integration DEV branchée sur le dossier `fabric`.
- Types d'items, règles, `framework.items`, connexions Fabric, Variable Libraries, `guid_policy`
  (sandbox, `allowed`) et `data_dependencies`, après inventaire (scripts 01 à 04). L'ancien
  `deploy.py` déclarait un lakehouse `lkh_storage_<ENV>` par environnement : nom à vérifier.
- Ancienne CD : ses workspaces ITG, PPR et PRD sont cohérents avec la liste des workspaces ;
  à désactiver une fois la nouvelle CD opérationnelle.
- Organisation et service connection GitHub ; noms des 3 service connections ARM ITG, PPR et PRD
  (création prévue).
- Nom et casse exacts du pipeline CI dans Azure DevOps (`its-dp-fab-opportunite-managed-CI`, sans
  « bdp- ») ; nom exact de la CD Framework.
- Approbations sur les Environments ADO `fabric-ppr` et `fabric-prd`.
- Point V-01 : le filtre `contents: "!/*.json"` de l'artefact CI ne doit pas exclure les fichiers d'items.
- Ajout des workspaces BDP Opportunite et Framework DEV au registre du socle, au prochain tag.
