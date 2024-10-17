---
title: "FONDAMENTAUX DES RÉSEAUX 1"
author: yassine
categories: [RÉSEAUX]
tags: [IP,OSI,RÉSEAUX]
render_with_liquid: false
img_path: /images/tryhackme_pyrat/
image:
  path:  /images/reseaux/Image2.png
---

## Introduction
Un `réseau` est un ensemble d'appareils connectés entre eux pour échanger des `données` et partager des `ressources` via un support physique (``câbles``) ou des connexions sans fil (``internet``).


![Desktop View](/images/reseaux/Image1.png){: width="80%" height="589" }


## IP ADDRESS

- Une adresse IP peut être utilisée pour identifier un hôte sur un réseau
- adresse IP est un ensemble de nombres divisés en quatre octets
- adresse IP fonctionne à la couche 3 du modèle OSI.

![Desktop View](/images/reseaux/Image3.png){: width="80%" height="589" }

### IP ADDRESS PARTIE

-  ``Partie réseau (Network)`` : La partie réseau d\'une adresse IP identifie le réseau auquel l\'appareil appartient. Tous les appareils sur le même réseau partagent cette portion de l\'adresse

- `Partie hôte (Host)` : La partie hôte d\'une adresse IP identifie un appareil spécifique (hôte) sur le réseau. Cette portion est unique pour chaque appareil dans le réseau
![Desktop View](/images/reseaux/Image4.png){: width="80%" height="589" }


### IP ADDRESS CLASSES
Les adresses IP sont classées en différentes classes pour faciliter leur organisation et leur utilisation dans les réseaux.
Elles sont divisées en cinq classes principales : ``A, B, C, D, et E``.
- Les classes A, B et C sont les plus couramment utilisées pour les réseaux publics et privés. 
- La classe ``A``, avec un premier octet compris entre ``1 et 126``, est destinée aux grands réseaux.
- La classe ``B``, dont le premier octet est entre ``128 et 191``, est utilisée pour les réseaux de taille moyenne. 
- La classe ``C``, avec un premier octet entre ``192 et 223``, est réservée aux petits réseaux.
Les classes ``D`` et ``E`` sont utilisées pour le multicast et la recherche expérimentale, respectivement.
![Desktop View](/images/reseaux/Image5.png){: width="80%" height="589" }

### PRIVATE AND PUBLIC IP

Les adresses IP **publiques** et **privées** sont utilisées pour identifier des appareils sur un réseau, mais elles ont des fonctions différentes.

- **Adresses IP publiques** : Elles sont attribuées par un fournisseur de services Internet (FAI) et sont utilisées pour identifier de manière unique un appareil sur l’Internet global. Elles permettent à des appareils d'un réseau local de communiquer avec l’extérieur. Par exemple, un site web ou un serveur de messagerie utilise une adresse IP publique pour être accessible à tous les utilisateurs sur Internet.

- **Adresses IP privées** : Elles sont utilisées au sein d’un réseau local (LAN) et ne peuvent pas être routées sur Internet. Ces adresses sont réservées pour la communication interne dans des réseaux domestiques ou d'entreprise, et elles ne sont pas uniques en dehors de ce réseau. Des plages spécifiques sont réservées pour les adresses privées : 10.0.0.0 à 10.255.255.255, 172.16.0.0 à 172.31.255.255, et 192.168.0.0 à 192.168.255.255.

En résumé, les **IP publiques** permettent l’accès à Internet, tandis que les **IP privées** facilitent la communication à l’intérieur d’un réseau local.
![Desktop View](/images/reseaux/Image6.png){: width="80%" height="589" }


### IPV4 VS IPV6

- IPv4: Bits: 32 bits
    - nombre possible des addresses: ``2^32 = 4,294,967,296`` 
    - (*about 4.3 billion*)
    
- IPv6:Bits: 128 bits
    - nombre possible des addresses: ``2^128 = 340,282,366,920,938,463,463,374,607,431,768,211,456`` 
    - (*about 340 undecillion*)
    
Par exemple :
![Desktop View](/images/reseaux/Image7.png){: width="80%" height="589" }


## MAC ADDRESS

- Un identifiant unique, chaque appareil possède une adresse MAC unique.
- Fonctionne à la couche liaison de données (couche 2) du modèle OSI.
![Desktop View](/images/reseaux/Image8.png){: width="80%" height="589" }



## PORTS
Les **ports** sont des numéros logiques utilisés pour identifier des services spécifiques sur un appareil réseau, permettant ainsi la communication entre différents dispositifs via des protocoles tels que TCP et UDP. Chaque port est associé à une application ou un service, ce qui permet de diriger les données au bon endroit.

Les ports sont classés en plusieurs catégories :

- **Ports bien connus (Well-Known Ports)** : Ils vont de 0 à 1023 et sont généralement réservés pour des services standardisés comme le port 80 pour HTTP, le port 443 pour HTTPS, et le port 22 pour SSH.
  
- **Ports enregistrés (Registered Ports)** : De 1024 à 49151, ces ports sont utilisés par des applications ou services spécifiques enregistrés auprès de l'IANA (Internet Assigned Numbers Authority). Ils ne sont pas universellement standardisés comme les ports bien connus, mais sont largement utilisés.

- **Ports dynamiques/privés (Dynamic/Private Ports)** : De 49152 à 65535, ils sont utilisés temporairement par des applications pour des communications internes, comme lors de l'établissement d'une connexion sortante.

Les ports permettent de gérer plusieurs connexions sur un même appareil en attribuant un port spécifique à chaque service ou application en cours d'exécution.
![Desktop View](/images/reseaux/Image9.png){: width="80%" height="589" }


## MODÈLE OSI

Un modèle est un ensemble de techniques qui définit comment les appareils communiquent dans un réseau. Un modèle standard comme l\'OSI `garantit que les appareils de différents fabricants peuvent se connecter`. Par exemple, sans modèle standard, les ordinateurs Windows et Mac pourraient ne pas communiquer correctement en raison de techniques de réseau différentes.

![Desktop View](/images/reseaux/Image10.png){: width="80%" height="589" }

# MODÈLE OSI COUCHES
Le modèle OSI (Open Systems Interconnection) est un modèle en 7 couches qui décrit le processus de communication entre deux systèmes sur un réseau. Chaque couche a une fonction spécifique et interagit avec les couches adjacentes.
![Desktop View](/images/reseaux/Image11.png){: width="80%" height="589" }


- # APPLICATION 
**Couche application**: C'est la couche la plus proche de l'utilisateur, qui permet aux applications de se connecter au réseau. Des protocoles comme HTTP, FTP, SMTP, et DNS fonctionnent à ce niveau.
![Desktop View](/images/reseaux/Image12.png){: width="80%" height="589" }

- # PRESENTATION 
**Couche présentation** : Elle s'occupe de la traduction des données entre différents formats, du chiffrement, de la compression, et de la conversion des données pour qu’elles soient compréhensibles par la couche application.
![Desktop View](/images/reseaux/Image13.png){: width="80%" height="589" }

- # SESSION 
**Couche session** : Elle établit, gère et termine les sessions de communication entre deux dispositifs. Elle synchronise les échanges de données et peut reprendre en cas d'interruption.
![Desktop View](/images/reseaux/Image14.png){: width="80%" height="589" }

- # TRANSPORT 
**Couche transport** : Elle garantit la livraison correcte et fiable des données entre deux hôtes via des protocoles comme TCP (fiable) et UDP (non fiable). Elle gère également la segmentation et le contrôle de flux.
![Desktop View](/images/reseaux/Image15.png){: width="80%" height="589" }

- # RÉSEAU 
**Couche réseau** : Cette couche gère l'adressage logique et l'acheminement des paquets à travers le réseau, notamment via des routeurs. Le protocole IP fonctionne à ce niveau.
![Desktop View](/images/reseaux/Image16.png){: width="80%" height="589" }

- # LIAISON DE DONNÉES
**Couche liaison de données** : Elle assure la communication fiable entre deux nœuds adjacents en détectant et corrigeant les erreurs. Elle est responsable du contrôle d'accès au support (MAC) et de l'adressage matériel (adresse MAC)
![Desktop View](/images/reseaux/Image17.png){: width="80%" height="589" }

- # PHYSIQUE 
**Couche physique** : C'est la couche qui s'occupe de la transmission des données brutes (bits) sur le support physique (câbles, fibres optiques, ondes radio, etc.). Elle concerne les aspects matériels du réseau.
![Desktop View](/images/reseaux/Image18.png){: width="80%" height="589" }

- # ENCAPSULATION

![Desktop View](/images/reseaux/Image19.png){: width="80%" height="589" }











