## Analyse de Statistiques League of Legends (Projet ELT)

Equipe  
Rémi TRAN SAMMARCELLI 
Mathieu VERMENOUZE
Théo TARDIVON

## Jeu de Données 
Source : Dataset League of Legends (CSV)  
Contenu : Plusieurs milliers de parties League of Legends.  
Caractéristiques :  
- Informations de parties (mode, durée, version)  
- Joueurs (puuid, pseudo, niveau)  
- Champions joués  
- Statistiques détaillées par participant  
- Classements SoloQ et Flex  

## Architecture Technique

Composant        | Technologie                  | Rôle
-----------------|------------------------------|---------------------------------------------
Extract / Load   | PostgreSQL 15                | Stockage des données RAW et transformées
Transformation  | Python (Pandas, SQLAlchemy)  | Nettoyage, typage et normalisation
Visualisation   | Metabase                     | Analyse et dashboards
Orchestration   | Docker Compose               | Conteneurisation de l’infrastructure

Installation et Lancement

### 1. Prérequis  
Docker Desktop et Python 3.10+  

Placer le fichier CSV dans le dossier `./data/`

### 2. Démarrage de l’infrastructure
```bash
docker compose up -d

### 3. Exécution de l’ETL  
Ouvrir et exécuter le notebook ETL.ipynb.
Celui-ci automatise l’importation des données (RAW), la transformation relationnelle et la création du Data Mart.

### 4. Modèle relationnel
Le modèle relationnel permet d’organiser les données League of Legends de manière normalisée et cohérente.

La table games contient les informations générales des parties.
La table summoners stocke les données des joueurs, tandis que champions référence les champions joués.

La table participants fait le lien entre une partie, un joueur et un champion, et décrit le contexte de jeu (rôle, équipe, victoire).
Les statistiques détaillées par joueur et par partie sont stockées dans participant_stats.

Enfin, la table ranks conserve les informations de classement des joueurs en SoloQ et Flex.

Ce modèle sert de base à la création du Data Mart et aux analyses ultérieures.