# 🤖 NovaTech RAG

## 📌 Présentation

**NovaTech RAG** est un chatbot intelligent basé sur l'architecture **Retrieval-Augmented Generation (RAG)**.

L'application permet aux utilisateurs de poser des questions concernant **NovaTech Solutions**. Le système recherche d'abord les informations pertinentes dans les documents internes, puis utilise **Gemini** pour générer une réponse basée uniquement sur les informations récupérées.

Le projet intègre également un **garde-fou de sécurité** afin de bloquer certaines requêtes sensibles.

---

## 🏗️ Architecture du projet

```text
NovaTech-RAG/
│
├── app.py                  # Interface utilisateur avec Gradio
├── rag.py                  # Logique complète du système RAG
├── config.py               # Configuration et variables d'environnement
├── requirements.txt        # Dépendances Python
├── Dockerfile              # Configuration de l'image Docker
├── .gitignore              # Fichiers exclus de Git
│
└── documents/
    ├── entreprise.txt
    ├── reglement.txt
    ├── conges.txt
    ├── faq.txt
    └── securite_test.txt
```

---

## 🔄 Fonctionnement du système RAG

Le fonctionnement de l'application suit les étapes suivantes :

```text
Documents
    ↓
Chargement des documents
    ↓
Découpage en chunks
    ↓
Génération des embeddings
    ↓
Indexation avec FAISS
    ↓
Question utilisateur
    ↓
Recherche des chunks pertinents
    ↓
Construction du contexte
    ↓
Prompt
    ↓
Gemini
    ↓
Réponse
```

### 1. 📄 Chargement des documents

Les documents présents dans le dossier `documents/` sont automatiquement chargés par l'application.

### 2. ✂️ Découpage en chunks

Les documents sont divisés en petits morceaux afin de faciliter la recherche d'informations pertinentes.

* Taille d'un chunk : **400 caractères**
* Overlap : **50 caractères**

### 3. 🧠 Embeddings

Chaque chunk est transformé en vecteur à l'aide du modèle :

```text
all-MiniLM-L6-v2
```

### 4. 🔎 Recherche avec FAISS

Les embeddings sont stockés dans un index **FAISS** permettant de rechercher rapidement les passages les plus similaires à la question.

Le système récupère les **3 chunks les plus pertinents**.

### 5. 🤖 Génération avec Gemini

Les informations récupérées sont envoyées à **Gemini 2.5 Flash** avec des instructions précises afin que le modèle réponde uniquement à partir du contexte fourni.

### 6. 🛡️ Garde-fou de sécurité

Avant la recherche, certaines requêtes sensibles sont détectées et bloquées.

Exemple :

```text
Comment voler un mot de passe ?
```

Le système retourne :

```text
Je ne peux pas fournir ou rechercher des informations sensibles.
```

---

## 🧰 Technologies utilisées

| Technologie           | Utilisation                    |
| --------------------- | ------------------------------ |
| Python                | Langage principal              |
| Gradio                | Interface utilisateur          |
| Gemini                | Génération des réponses        |
| FAISS                 | Recherche vectorielle          |
| Sentence Transformers | Génération des embeddings      |
| LangSmith             | Traçage et observabilité       |
| Docker                | Conteneurisation               |
| GitHub Codespaces     | Environnement de développement |

---

## 📦 Installation

Cloner le projet :

```bash
git clone https://github.com/USERNAME/NovaTech-RAG.git
cd NovaTech-RAG
```

Installer les dépendances :

```bash
pip install -r requirements.txt
```

---

## 🔐 Configuration des clés API

Le projet utilise deux clés API :

```text
GOOGLE_API_KEY
LANGSMITH_API_KEY
```

Ces clés doivent être configurées comme **variables d'environnement**.

### Google Gemini

La clé `GOOGLE_API_KEY` est utilisée pour communiquer avec l'API Gemini.

### LangSmith

La clé `LANGSMITH_API_KEY` permet d'activer le traçage des différentes étapes du système RAG.

Les clés API ne doivent jamais être ajoutées directement dans le code ou publiées sur GitHub.

---

## ▶️ Lancer l'application

Pour démarrer l'application :

```bash
python app.py
```

L'application Gradio utilise le port :

```text
7860
```

Dans GitHub Codespaces, il suffit ensuite d'ouvrir le port **7860** pour accéder à l'interface.

---

## 🐳 Utilisation avec Docker

### Construire l'image

```bash
docker build -t novatech-rag .
```

### Vérifier l'image

```bash
docker images
```

### Lancer le conteneur

```bash
docker run --rm \
  -p 7860:7860 \
  -e GOOGLE_API_KEY="$GOOGLE_API_KEY" \
  -e LANGSMITH_API_KEY="$LANGSMITH_API_KEY" \
  -e LANGSMITH_TRACING=true \
  -e LANGSMITH_PROJECT=NovaTech-RAG \
  novatech-rag
```

L'application sera accessible sur le port :

```text
7860
```

---

## 📊 LangSmith

Le projet utilise **LangSmith** pour suivre les différentes étapes du pipeline RAG.

Les traces permettent notamment d'observer :

```text
NovaTech RAG
      │
      ├── Recherche FAISS
      │
      ├── Construction du contexte
      │
      ├── Création du prompt
      │
      └── Appel Gemini
```

Cela permet de mieux comprendre et déboguer le comportement du système.

---

## 🛡️ Sécurité

Le projet contient un mécanisme simple de **guardrail**.

Certaines requêtes contenant des informations sensibles sont bloquées avant leur traitement.

Exemples de termes surveillés :

```text
mot de passe
password
secret
confidentiel
identifiants
credential
```

L'objectif est d'éviter que le système recherche ou génère des informations sensibles.

---

## 🧪 Exemple d'utilisation

### Question

```text
Combien de jours de congé sont disponibles ?
```

### Traitement

```text
Question
   ↓
Embedding
   ↓
Recherche FAISS
   ↓
Top 3 résultats
   ↓
Contexte
   ↓
Gemini
```

### Résultat

L'assistant génère une réponse basée sur les informations présentes dans les documents de NovaTech Solutions.

Les sources récupérées sont également affichées dans l'interface.

---

## 📁 Ajouter de nouveaux documents

Pour ajouter de nouvelles informations à la base de connaissances, il suffit d'ajouter des fichiers `.txt` dans :

```text
documents/
```

Exemple :

```text
documents/
├── entreprise.txt
├── reglement.txt
├── conges.txt
├── faq.txt
├── securite_test.txt
└── recrutement.txt
```

Les documents sont automatiquement chargés au démarrage de l'application.

---

## 🚀 Améliorations possibles

Le projet peut être amélioré avec :

* 📄 Support des fichiers PDF, Word et Excel
* 🔎 Recherche hybride
* 🧠 Meilleurs modèles d'embeddings
* 💾 Base vectorielle persistante
* 🔐 Guardrails plus avancés
* 📊 Évaluation automatique du RAG
* ☁️ Déploiement sur le cloud
* 👤 Authentification des utilisateurs
* 📈 Monitoring avancé avec LangSmith

---

## 👩‍💻 Auteur

**Maissa Daas**

Projet réalisé dans le cadre d'un projet de chatbot **RAG pour NovaTech Solutions**.

---

## 📄 Licence

Ce projet est destiné à des fins éducatives et de démonstration.
