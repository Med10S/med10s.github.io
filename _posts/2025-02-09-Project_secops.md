---
title: "Défense Réseau 2.0 : La Résilience face aux Attaques DDoS Internes"
author: med
categories: [cybersécurité]
tags: [elasticsearch,logstash,docker,kibana,GNS3]
render_with_liquid: false
math: true

img_path: /images/secosp_P1/
image:
  path:  /images/secosp_P1/Home.jpg
---




### **Introduction au Projet :**

**Salut à tous !**

Ce semestre, nous allons plonger dans un projet captivant centré sur la cybersécurité, avec un accent sur les attaques DDoS et les méthodes de défense. **TechCorp**, notre entreprise fictive, fera face à une attaque provenant d'un employé malveillant ou d'un dispositif compromis dans son réseau interne. Nous apprendrons à détecter, analyser et contrer ce type d'attaque en utilisant des outils modernes.

Ce projet est crucial pour nous, étudiants en cybersécurité, car il nous permettra de comprendre les mécanismes d'une **attaque DDoS** et de développer des compétences clés dans la gestion des incidents de sécurité, l'analyse des logs et l'utilisation de solutions comme **Suricata** et **ELK Stack**. En utilisant **ELK Stack** (Elasticsearch, Logstash, Kibana), nous vivrons des expériences similaires à celles des analystes de **SOC** (Security Operations Center) en collectant, analysant et visualisant les données de logs.

Avant de passer à la pratique, il est essentiel de **maîtriser la théorie** derrière ces concepts et de bien comprendre les outils que nous allons utiliser.

---


### **Plan du Projet :**

---

#### **Étape 1 : Introduction à la Sécurité Réseau et à GNS3**

- **Objectif :** Découvrir les bases de la sécurité réseau, les attaques courantes (notamment DDoS) et comprendre pourquoi il est essentiel de sécuriser nos réseaux.
  
- **Concepts théoriques à aborder :**
  - **Les attaques DDoS :** Qu'est-ce qu'une attaque DDoS ? Comment elle fonctionne ? Pourquoi ces attaques peuvent être dévastatrices pour les entreprises ? 
  - **Les VLANs (Virtual LANs) :** Pourquoi utiliser des VLANs pour segmenter un réseau ? Comment cela aide-t-il à sécuriser l'infrastructure ?
  - **La notion de pare-feu et de filtrage :** Comment protéger un réseau en utilisant des pare-feu pour filtrer le trafic ?
  
- **Présentation de GNS3 et GNS3 VM :**
  - **GNS3** est un simulateur de réseau qui nous permet de créer des topologies complexes sans avoir besoin de matériel physique. Cela va nous permettre de simuler l'attaque et la défense sur notre propre réseau virtuel.
  - **GNS3 VM** est essentiel car il permet de faire tourner des machines virtuelles (VM) nécessaires pour les simulations de réseau. Nous allons l’installer pour exécuter nos conteneurs et machines virtuelles.
  - **Pourquoi GNS3 et GNS3 VM sont essentiels pour ce projet ?** Ces outils nous permettront de créer un environnement sécurisé et de simuler notre réseau d'entreprise TechCorp, avec des machines et des équipements réels, mais sur un environnement virtuel. 

- **Installation de GNS3 et GNS3 VM :**
  Avant de commencer, chacun doit installer **GNS3** et **GNS3 VM** sur son ordinateur. Si vous ne l'avez pas encore fait, assurez-vous de suivre les instructions d'installation disponibles sur le site officiel de GNS3.

  | Besoin                 | Système d'exploitation | Lien de téléchargement                                                         |
  |------------------------|------------------------|--------------------------------------------------------------------------------|
  | GNS3                   | Windows                | [Télécharger](https://docs.gns3.com/docs/getting-started/installation/windows/#download-the-gns3-all-in-one-installer){:target="_blank"} |
  | GNS3                   | macOS                  | [Télécharger](https://docs.gns3.com/docs/getting-started/installationmac){:target="_blank"}      |
  | GNS3                   | Linux                  | [Télécharger](https://docs.gns3.com/docs/getting-started/installationlinux){:target="_blank"}    |
  
  |----------|---------------|----------------------------------------------------------------------------------------|
  | GNS3 VM  | Documentation | [Visiter](https://docs.gns3.com/docs/getting-started/installation/download-gns3-vm){:target="_blank"}|
  
  - GNS3 nécessite **VirtualBox** ou **VMware** pour exécuter les machines virtuelles (GNS3 VM).
  - Une fois tout installé, nous vérifierons que vous avez bien accès à GNS3 et GNS3 VM.


---

#### **Étape 2 : Configuration et Sécurisation du Réseau de TechCorp**

- **Objectif :** Créer la topologie du réseau TechCorp, y compris la configuration des VLANs, des serveurs et des routeurs, et comprendre comment segmenter et sécuriser un réseau.

- **Concepts théoriques à aborder :**
  - **Les VLANs** : Qu'est-ce qu'un VLAN ? Pourquoi sont-ils importants pour la sécurité d'un réseau d'entreprise ?
  - **Les bases de la segmentation de réseau** : La segmentation permet d’isoler les différents segments de réseau (ex. : VLAN pour les utilisateurs, VLAN pour les serveurs, VLAN pour l’administration) afin de limiter les impacts en cas d'attaque.
  - **DHCP** : Pourquoi attribuer dynamiquement des adresses IP dans une entreprise ?
  - **Firewall** : Qu'est-ce qu'un pare-feu et pourquoi il est crucial dans la défense contre les attaques ?

- **Dans GNS3 :** 
  - Créez une **topologie réseau** où vous allez :
    - Configurer des **routeurs** et **switches** pour interconnecter les VLANs.
    - Créer des **VLANs** pour séparer le réseau (administration, utilisateurs, serveurs).
    - Configurer un serveur **DHCP** pour gérer les adresses IP dynamiques dans chaque VLAN.
    - Ajouter un **firewall** pour protéger le réseau contre les menaces externes.

- **Installation et Configuration du Pare-feu :**
  Nous installerons un **firewall** pour limiter le trafic entrant et sortant, ce qui est une étape clé pour se protéger contre des attaques comme les DDoS. Nous vous guiderons dans sa configuration, mais il est crucial que chacun de vous télécharge les images de base du routeur et du switch pour commencer à construire la topologie.


| SR | 	CISCO IOS NAME | DOWNLOAD | Needed |
|:------:|------------|:---------:|:---------:|
| 1 | vIOS-L2.vmdk  |[Download here](https://drive.google.com/file/d/1QT7dqDeaQsTnzM7jD-_9ucr1p6mxLZh0/view?usp=drive_link) |as a switch| 
| 2 | c7200-adventerprisek9-mz.124-24.T5.image  |[Download here](https://mega.nz/#!RZtA0SwD!XBjqI5Dkrienz7tHaYg601Dwq-ypAqWZv8Ut3mFuKoI) |as a router |


---

#### **Étape 3 : Introduction aux Attaques DDoS et Simulation avec Hping**

- **Objectif :** Apprendre ce qu’est une attaque DDoS et comment simuler cette attaque depuis l’intérieur du réseau de TechCorp.

- **Concepts théoriques à aborder :**
  - **Les attaques DDoS** : Comment une attaque DDoS fonctionne-t-elle et comment elle peut submerger un serveur ? Quels sont les types d’attaques DDoS (SYN Flood, UDP Flood, etc.) ?
  - **L'impact d'une attaque DDoS sur un réseau d'entreprise** : Comprendre l'effet d'une telle attaque sur les services web, bases de données, etc.
  - **L'outil Hping** : Un outil en ligne de commande utilisé pour simuler des attaques DDoS. Nous l'utiliserons pour tester la résistance de notre réseau à ces attaques.

- **Dans GNS3 :**
  - Créer un **attaquant interne** (utilisateur compromis dans TechCorp) et un **serveur cible**.
  - Utiliser **Hping** pour envoyer un **SYN flood** vers le serveur.
  
- **Exercices pratiques :**
  - Simulation d’une attaque DDoS sur le serveur cible. Vous allez envoyer un trafic intense vers le serveur pour le rendre indisponible.

---

#### **Étape 4 : Détection des Attaques avec Suricata**

- **Objectif :** Installer Suricata pour analyser le trafic réseau et détecter une attaque DDoS en temps réel.

- **Concepts théoriques à aborder :**
  - **Qu'est-ce qu'un IDS (Intrusion Detection System) ?** Pourquoi utiliser un IDS comme **Suricata** pour détecter les menaces dans le trafic réseau ?
  - **Les signatures d’attaque** : Comment Suricata utilise des signatures pour identifier les attaques ? Que sont les alertes et comment les interpréter ?

- **Installation et configuration de Suricata :**
  Vous allez installer **Suricata** sur un contenaire Docker dans la VM de GNS3 qui seras dédiée à la détection des attaques et configurer ce système pour surveiller le réseau et détecter les attaques DDoS.
  
#### **Étape 5 : Visualisation des Attaques avec l'ELK Stack**


- **Objectif :** Analyser les logs de l'attaque en temps réel à l’aide de l'ELK Stack (Elasticsearch, Logstash, Kibana).

- **Concepts théoriques à aborder :**
  - **ELK Stack** : Qu’est-ce que c’est ? Comment ces outils peuvent-ils nous aider à collecter et analyser des logs de manière efficace ?
  - **Kibana pour la visualisation** : Comment créer des dashboards interactifs pour afficher les alertes générées par Suricata ?
  - **Alertes et rapports** : Comment configurer Kibana pour générer des rapports et alertes en fonction des attaques détectées ?

- **Dans GNS3 :**
  - **Déployer l’ELK Stack** sur des machines virtuelles via Docker dans GNS3 VM. Assurez-vous que **Elasticsearch**, **Logstash** et **Kibana** sont bien installés et fonctionnels.
  - **Collecte des logs de Suricata** : Nous configurerons Logstash pour récupérer les logs générés par Suricata et les envoyer à Elasticsearch pour une analyse plus approfondie.

---

#### **Étape 6 : Réponse aux Incidents et Conclusion**

- **Objectif :** Réagir face à une attaque détectée et préparer un plan de réponse aux incidents.

- **Concepts théoriques à aborder :**
  - **La gestion des incidents de sécurité** : Comment réagir face à une alerte de sécurité ? Quels sont les processus à suivre ?
  - **La prévention future** : Que faire après l'attaque pour prévenir de futures intrusions ?

- **Discussion :**
  Nous discuterons de la manière dont le système de défense mis en place peut être amélioré. Nous parlerons aussi de la manière

 dont ce genre de projet peut être mis en œuvre dans un environnement professionnel réel.

---

### **Conclusion**

À travers ce projet, vous allez acquérir des compétences pratiques en sécurité réseau et en réponse aux incidents, des connaissances cruciales pour protéger les infrastructures des entreprises contre les menaces modernes. Vous comprendrez également pourquoi chaque étape — de la configuration réseau à la détection et l’analyse des attaques — est essentielle pour construire un système de défense robuste.