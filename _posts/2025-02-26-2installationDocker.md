---
title: "Installation & Premiers Pas avec Docker"
date: 2025-02-26
categories: [blog]
img_path: /images/docker/
image:
  path:  /images/docker/home.webp
sub_articles:
  - title: "Introduction à l'Écosystème Docker"
    url: "posts/3Conteneur_Docker/"
    excerpt: "L'écosystème Docker comprend plusieurs outils et technologies permettant de créer, déployer, gérer et orchestrer des conteneurs."
    date: "2025-02-26"
    category: "Docker"
    img_path: /images/vlan/
    image: "/images/vlan/15.png"
---


### ⚙️ Partie 2 : Installation & Premiers Pas avec Docker 


## 1. Installation sur **Windows 10/11 ** 🖥️
---

### Pré-requis :
- Activer la virtualisation dans le BIOS
- WSL2 installé

### Étapes :
1. Télécharger Docker Desktop :
👉 [Télécharger Docker Desktop](https://www.docker.com/products/docker-desktop/)

2. Installer Docker Desktop
3. Activer WSL2 lors de l'installation
4. Redémarrer le PC
5. Vérifier l'installation :
```bash
docker --version
```


---

## 2. Installation sur **macOS** 🍎
---

### Pré-requis :
- macOS 10.15 (Catalina) ou plus récent
- Chip **Intel** ou **Apple M1/M2**

### Étapes :
1. Télécharger Docker Desktop :
👉 [Télécharger Docker Desktop](https://www.docker.com/products/docker-desktop/)

2. Installer l'application
3. Lancer Docker depuis Applications
4. Vérifier :
```bash
docker --version
```


---

## 3. Vérification Finale 🔥
Quel que soit l'OS, lance cette commande :
```bash
docker run hello-world
```
✅ Si tu vois :
```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```
Docker est bien installé 🎯

### 👇 Suivant: