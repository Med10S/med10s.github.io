---
title: "Dockerfile"
date: 2025-02-26
hidden: true 
categories: [blog]
img_path: /images/docker/
image:
  path:  /images/docker/home.webp

---
# Formation Docker - Partie 1 : Dockerfile

## 1. Introduction

Un **Dockerfile** est un fichier texte contenant une série d'instructions permettant de créer automatiquement une image Docker. Il constitue le plan de construction pour la création d'images légères, portables et reproductibles. L'utilisation d'un Dockerfile est essentielle pour automatiser la création d'environnements de développement, de test ou de production.

### Objectifs :
- Comprendre la structure d'un Dockerfile et ses instructions fondamentales
- Créer une image Docker personnalisée à partir d'un projet
- Optimiser l'image pour améliorer la performance, la sécurité et la taille
- Appliquer des bonnes pratiques pour assurer la portabilité et la réutilisabilité

---

## 2. Structure de base d'un Dockerfile

Un Dockerfile est composé d'instructions exécutées séquentiellement pour construire l'image finale. Chaque instruction crée une nouvelle couche dans l'image Docker, rendant la structure modulaire et optimisée.

### Principales Instructions :

| Instruction  | Description                                       | Usage recommandé                                                               |
| ------------ | ------------------------------------------------- | ------------------------------------------------------------------------------ |
| `FROM`       | Définit l'image de base pour la construction      | Toujours commencer par cette instruction avec une image légère (ex : `alpine`) |
| `RUN`        | Exécute des commandes dans l'image                | Grouper plusieurs commandes pour réduire les couches                           |
| `COPY`       | Copie des fichiers locaux dans l'image            | Préférer à `ADD` sauf pour les archives compressées                            |
| `ADD`        | Copie des fichiers + extraction automatique       | Utiliser uniquement pour les fichiers compressés ou les URLs                   |
| `WORKDIR`    | Définit le répertoire de travail par défaut       | Utiliser pour organiser la structure des dossiers                              |
| `CMD`        | Commande par défaut pour exécuter le conteneur    | Préférer `ENTRYPOINT` si la commande est non modifiable                        |
| `ENTRYPOINT` | Définir une commande principale pour le conteneur | Permet d'exécuter le conteneur comme une application                           |
| `EXPOSE`     | Indique quel port sera exposé                     | Documenter les ports utilisés pour la communication                            |
| `ENV`        | Définit des variables d'environnement             | Permet de configurer l'application via des variables                           |
| `VOLUME`     | Crée des volumes pour la persistance de données   | Toujours utiliser pour les données persistantes                                |
| `LABEL`      | Ajoute des métadonnées                            | Permet d'ajouter des informations sur l'image                                  |

### Commentaire :

L'ordre des instructions est important pour maximiser l'efficacité du cache Docker.

---

## 3. Exemple de Dockerfile

Voici un exemple simple pour une application Node.js avec quelques optimisations :

```dockerfile
# Utilisation de l'image de base légère
FROM node:18-alpine

# Définir le répertoire de travail
WORKDIR /app

# Copier uniquement les fichiers de dépendances pour optimiser le cache Docker
COPY package*.json ./

# Installer les dépendances en mode production
RUN npm install --production && npm cache clean --force

# Copier le reste des fichiers de l'application
COPY . .

# Définir une variable d'environnement
ENV NODE_ENV=production

# Exposer le port utilisé par l'application
EXPOSE 3000

# Définir la commande de lancement de l'application
CMD ["node", "server.js"]
```

---

## 4. Construction de l'image

Pour construire l'image, utilisez la commande :

```bash
docker build -t mon-app:1.0 .
```

### Explication :

- `-t mon-app:1.0` : Donne un nom et une version à l'image
- `.` : Indique que le Dockerfile est dans le répertoire courant

#### Vérification de l'image créée :

```bash
docker images
```

---


## ⚠️⚠️ Différence entre Dockerfile et docker-compose.yml

### Dockerfile

👉 Un **Dockerfile** est un fichier texte contenant une série d'instructions pour créer une image Docker. Il définit les étapes nécessaires pour construire une image, incluant l'installation des dépendances, la configuration de l'environnement et la copie des fichiers.

### docker-compose.yml

👉 Un fichier **docker-compose.yml** est utilisé pour définir et gérer des applications multi-conteneurs. Il permet de décrire les services, les réseaux et les volumes nécessaires pour une application. Avec Docker Compose, vous pouvez démarrer, arrêter et configurer plusieurs conteneurs en une seule commande.

### Comparaison

| Aspect                | Dockerfile                                      | docker-compose.yml                                      |
|-----------------------|-------------------------------------------------|---------------------------------------------------------|
| Usage                 | Créer une image Docker                          | Orchestrer plusieurs conteneurs                          |
| Format                | Fichier texte avec des instructions Docker      | Fichier YAML décrivant les services                      |
| Commande principale   | `docker build`                                  | `docker-compose up`                                      |
| Portée                | Image unique                                    | Application multi-conteneurs                             |
| Configuration réseau  | Non géré                                        | Gère les réseaux entre les conteneurs                    |
| Volumes               | Définis avec l'instruction `VOLUME`             | Définis dans la section `volumes`                        |
| Variables d'environnement | Définies avec l'instruction `ENV`           | Définies dans la section `environment`                   |

### Exemple de docker-compose.yml

```yaml
version: '3'
services:
  web:
  # mon-app:1.0  une image que tu a creer avec DockerFile ou telecharger depuis DockerHub 
    image: mon-app:1.0 
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    environment:
      - NODE_ENV=production
  db:
    image: postgres:13
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=secret

volumes:
  db-data:
```

En résumé, le **Dockerfile** est utilisé pour créer des images Docker, tandis que le **docker-compose.yml** est utilisé pour orchestrer et gérer des applications multi-conteneurs.

## 5. Bonnes pratiques pour optimiser l'image

- Utiliser des images de base minimales (ex : `alpine`, `slim`) pour réduire la taille
- Grouper plusieurs commandes dans une seule instruction `RUN`
- Supprimer les fichiers temporaires après installation pour limiter la taille
- Utiliser des fichiers `.dockerignore` pour exclure les fichiers inutiles (ex : `node_modules`, `.git`, `.env`)
- Définir des variables d'environnement pour éviter de stocker des secrets directement dans l'image
- Utiliser des labels pour documenter l'image
- Tester les images avec des outils comme **Dive** pour analyser la taille des couches

Exemple de fichier `.dockerignore` :

```
node_modules
.git
.env
logs
__pycache__
*.log
```

---

## 6. Sécurité dans les Dockerfiles

- Ne jamais stocker des mots de passe ou des clés API en dur
- Utiliser des variables d'environnement pour la configuration
- Scanner les images avec des outils comme **Trivy** pour détecter les vulnérabilités
- Maintenir les images à jour avec les dernières versions

---

## 7. Conclusion

Le **Dockerfile** est l'élément central dans la création d'images Docker. En adoptant les bonnes pratiques, il permet de créer des images légères, sécurisées et reproductibles. Une bonne maîtrise du Dockerfile garantit des déploiements plus rapides et une meilleure portabilité des applications.


## Structure des dossiers et fichiers

Voici comment organiser les fichiers pour un projet Docker :

```
.
├── Dockerfile          # Fichier de configuration Docker
├── .dockerignore       # Fichier pour ignorer les fichiers inutiles
├── package.json        # Dépendances du projet
├── package-lock.json   # Versions verrouillées des dépendances
├── src/               # Code source de l'application
│   └── server.js      # Fichier principal de l'application
└── logs/              # Logs de l'application
```

Cette structure permet d'organiser efficacement les fichiers tout en optimisant la taille et la performance de l'image Docker.