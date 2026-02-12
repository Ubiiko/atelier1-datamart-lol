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
<img width="734" height="1021" alt="modele" src="https://github.com/user-attachments/assets/0eae534e-344c-4836-b0eb-414e79c14291" />

## Table de faits
```fact_game_participation ```
Cette table représente la performance d’un joueur dans une partie.
Granularité :1 ligne = 1 joueur dans 1 partie.

Mesures principales :
```
win  
kills  
deaths  
assists  
total_damage_dealt  
gold_earned  
game_duration
```

Cette structure permet de calculer des indicateurs tels que le taux de victoire, le KDA moyen ou la durée moyenne des parties.

## Dimensions
Le modèle comporte cinq dimensions :
```
dim_time : analyse temporelle (jour, mois, année, week-end)
dim_player : informations sur le joueur (identité, rang, tier)
dim_champion : caractéristiques du champion (nom, classe)
dim_map : carte jouée
dim_game_mode : type et mode de jeu
```
Ces dimensions permettent d’analyser les performances selon différents axes métier.

# Exemple de question métier
Ce modèle permet de répondre à des questions telles que :

Quel est le taux de victoire par classe de champion sur une carte donnée durant le week-end ?

La table de faits fournit la mesure win, tandis que les dimensions permettent la segmentation par temps, champion et carte.

```mermaid
erDiagram
    FACT_GAME_PARTICIPATION {
        int time_sk
        int player_sk
        int champion_sk
        int map_sk
        int game_mode_sk
        boolean win
        int kills
        int deaths
        int assists
        int total_damage_dealt
        int gold_earned
        int game_duration
    }

    DIM_TIME {
        int time_sk
        date date
        int day
        int month
        int year
        string day_of_week
        boolean is_weekend
    }

    DIM_PLAYER {
        int player_sk
        string summoner_id
        string summoner_name
        string rank
        string tier
    }

    DIM_CHAMPION {
        int champion_sk
        int champion_id
        string champion_name
        string champion_class
    }

    DIM_MAP {
        int map_sk
        int map_id
        string map_name
    }

    DIM_GAME_MODE {
        int game_mode_sk
        string queue_type
        string game_mode
    }

    FACT_GAME_PARTICIPATION }o--|| DIM_TIME : time_sk
    FACT_GAME_PARTICIPATION }o--|| DIM_PLAYER : player_sk
    FACT_GAME_PARTICIPATION }o--|| DIM_CHAMPION : champion_sk
    FACT_GAME_PARTICIPATION }o--|| DIM_MAP : map_sk
    FACT_GAME_PARTICIPATION }o--|| DIM_GAME_MODE : game_mode_sk

