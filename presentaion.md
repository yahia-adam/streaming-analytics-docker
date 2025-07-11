
# 🎓 Présentation de Projet : **Streaming & Visualisation avec Docker**

---

## 🔎 Contexte

* **École** : ESGI
* **Niveau** : 4e année
* **Filière** : Intelligence Artificielle & Big Data
* **Cours** : Conteneurisation logicielle (Docker)
* **Équipe** :

  * TRAN DUC Bao Nguyen
  * BADIABO Divin
  * ELOY Théo
  * YAHIA ABDCHAFEE Adam

---

## 🎯 Objectif du projet

> Concevoir et déployer une **chaîne complète de traitement de données en streaming** conteneurisée avec **Docker Compose**, intégrant :

* Un **Producer** Spark qui lit les fichiers localement
* Un **Kafka broker** pour la transmission des données
* Un **Consumer** Spark qui consomme les données Kafka et les stocke en base de données
* Une **base PostgreSQL**
* Une application **Streamlit** pour la visualisation web

Le focus est mis sur :

* La **conteneurisation complète** des services
* La **gestion réseau sécurisée**
* L’**automatisation** avec Docker Compose
* Le **bon usage des volumes, images, variables d’environnement, sécurité réseau**

---

## 🏗️ Architecture technique

![Architecture du projet](attachment:/mnt/data/bd03109a-2a0e-4d7f-85f5-280c9e3b8e43.png)

### 🧱 Composants principaux

| Composant    | Fonction                                                           |
| ------------ | ------------------------------------------------------------------ |
| `Producer`   | Lit les fichiers Yelp localement et envoie les données dans Kafka  |
| `Kafka`      | Assure la distribution des messages (topic `yelp-topic-review`)    |
| `Consumer`   | Récupère les données Kafka et les stocke dans PostgreSQL via Spark |
| `PostgreSQL` | Stocke les résultats traités pour visualisation                    |
| `Streamlit`  | Interface web de visualisation                                     |
| `Zookeeper`  | Coordination du broker Kafka                                       |

---

## ⚙️ Infrastructure Docker

### 🔹 Orchestration avec Docker Compose

Tous les services sont orchestrés dans un fichier `docker-compose.yml`, incluant :

* **Construction des images custom (Producer / Consumer / Streamlit)**
* **Utilisation de volumes** : pour persistance (`postgres_data`) et partage de dataset
* **Utilisation de `.env`** pour sécuriser les accès à PostgreSQL
* **Utilisation de `depends_on`** pour gérer les ordres de démarrage

### 🔹 Réseaux Docker personnalisés

| Réseau       | Type   | Composants inclus         | But                                     |
| ------------ | ------ | ------------------------- | --------------------------------------- |
| `engnetwork` | Privé  | Kafka, Producer, Consumer | Communication interne sécurisée des ETL |
| `dbNetwork`  | Privé  | Consumer, PostgreSQL      | Connexion base de données sécurisée     |
| `webNetwork` | Public | Streamlit, PostgreSQL     | Accès externe à l'application web       |

> ⚠️ Les réseaux sont **isolés**, seuls les services qui doivent communiquer entre eux sont sur les mêmes réseaux.

---

## 🐳 Détails des Dockerfiles

### 🔸 Spark (Producer & Consumer)

* **Basé sur `openjdk:11`**
* Téléchargement et configuration de **Spark 3.5.0**
* Installation du `.jar` depuis les **GitHub Releases**
* Configuration JVM pour performance
* Commande de lancement : `spark-submit` avec dépendances Kafka + PostgreSQL

### 🔸 Streamlit

* **Basé sur `python:3.10`**
* Installation des dépendances via `requirements.txt`
* Exposition sur le port `8501`
* Code Python contenu dans `src/Welcome.py`

---

## 🧪 Démonstration & Jeu de données

* **Dataset** : portion de 5000 lignes issues de Yelp
* **Fichiers** : montés via volume Docker (`./yelp_dataset`)
* **Streamlit** : expose un dashboard interactif (mapping port `8501:8501`)
* **Spark UI** : exposé via port `4040` pour le Producer, `4041` pour le Consumer

---

## 📦 Gestion des images Docker

* Les images sont **construites localement** puis **poussées sur Docker Hub**
* Utilisation possible des images via Docker Compose en changeant le champ `image:` au lieu de `build:`

---

## ✅ Bonnes pratiques respectées

* 🔐 **Séparation des réseaux** public/privé
* 📁 **Utilisation de volumes** pour la persistance
* 🔄 **Redondance limitée** : Topics Kafka configurés (3 partitions, 1 réplica)
* 📜 **Configuration via `.env`** pour éviter le hardcoding
* 🔍 **Logs et accès UI exposés** pour debugging et supervision
* 📦 **Utilisation de GitHub Releases** pour versionner les `.jar` Spark
* 📊 **Visualisation claire** des résultats pour fin de pipeline

---

## 🔒 Sécurité & performances

| Aspect                | Implémentation                                                                 |
| --------------------- | ------------------------------------------------------------------------------ |
| Sécurité réseau       | Réseaux isolés, ports exposés uniquement pour Streamlit & Spark UI             |
| Variables sensibles   | Externalisées via `.env`                                                       |
| Ressources maîtrisées | Limites mémoire Spark (`--driver-memory`, `--executor-memory`)                 |
| Volume dataset        | Monté en lecture seule, permissions sécurisées sur les fichiers intermédiaires |

---

## 🚀 Conclusion

Ce projet met en pratique **l’ensemble des compétences fondamentales en conteneurisation** dans un contexte **Big Data / IA** :

* Mise en œuvre d’un **pipeline de streaming complet**
* Isolation des services via réseaux Docker
* Bonne gestion des volumes, images et configurations
* Déploiement d’une **application visualisable via le web**
* Application des **bonnes pratiques de sécurité et de performances**

