---
title: "Les fondamentaux de Linux 1"
author: yassine
categories: [linux]
tags: [linux]
render_with_liquid: false
img_path: /images/linux/
comments: true

image:
  path:  /images/linux/i2.png
---


## Qu’est-ce qu’un système d’exploitation ?

Linux est un système d’exploitation.

Un système d’exploitation est un ensemble de programmes permettant la gestion des ressources disponibles d’un ordinateur.

Parmi cette gestion des ressources, le système d’exploitation est amené à :

- Gérer la mémoire physique ou virtuelle.

    - La mémoire physique est composée des barrettes de mémoires vives et de la mémoire cache du processeur, qui sert pour l’exécution des programmes.

    - La mémoire virtuelle est un emplacement sur le disque dur (la partition swap) qui permet de décharger la mémoire physique et de sauvegarder l’état en cours du système durant l’arrêt électrique de l’ordinateur (hibernation du système).

- Intercepter les accès aux périphériques. Les logiciels ne sont que très rarement autorisés à accéder directement au matériel (à l’exception des cartes graphiques pour des besoins très spécifiques).

- Offrir aux applications une gestion correcte des tâches. Le système d’exploitation est responsable de l’ordonnancement des processus pour l’occupation du processeur.

- Protéger les fichiers contre tout accès non autorisé.

- Collecter les informations sur les programmes utilisés ou en cours d’utilisation.

![Desktop View](/images/linux/i3.png){: width="90%"  }_Figure1.Fonctionnement d’un système d’exploitation_

## Généralités UNIX - GNU/Linux
### Historique
**_UNIX_**
- De **1964** à **1968** : MULTICS (MULTiplexed Information and Computing Service) est développé pour le compte du MIT, des laboratoires Bell Labs (AT&T) et de General Electric.

- **1969** : Après le retrait de Bell (1969) puis de General Electric du projet, deux développeurs (Ken Thompson et Dennis Ritchie), rejoints plus tard par Brian Kernighan, jugeant MULTICS trop complexe, lancent le développement d’UNIX (UNiplexed Information and Computing Service). À l’origine développé en assembleur, les concepteurs d’UNIX ont développé le langage B puis le langage C (1971) et totalement réécrit UNIX. Ayant été développé en 1970, la date de référence des systèmes UNIX/Linux est toujours fixée au 01 janvier 1970.

Le langage C fait toujours partie des langages de programmation les plus populaires aujourd’hui ! Langage de bas niveau, proche du matériel, il permet l’adaptation du système d’exploitation à toute architecture machine disposant d’un compilateur C.

UNIX est un système d’exploitation ouvert et évolutif ayant joué un rôle primordial dans l’histoire de l’informatique. Il a servi de base pour de nombreux autres systèmes : Linux, BSD, Mac OSX, etc.

UNIX est toujours d’actualité (HP-UX, AIX, Solaris, etc.)

**_Minix_**
- **1987** : Minix. A.S. Tanenbaum développe MINIX, un UNIX simplifié, pour enseigner les systèmes d’exploitation de façon simple. M. Tanenbaum rend disponible les sources de son système d’exploitation.

_**Linux**_

- **1991** : Linux. Un étudiant finlandais, Linus Torvalds, crée un système d’exploitation dédié à son ordinateur personnel et le nomme Linux. Il publie sa première version 0.02, sur le forum de discussion Usenet et d’autres développeurs viennent ainsi l’aider à améliorer son système. Le terme Linux est un jeu de mot entre le prénom du fondateur, Linus, et UNIX.

- **1993** : La distribution Debian est créée. Debian est une distribution non commerciale à gestion associative. À l’origine développée pour une utilisation sur des serveurs, elle est particulièrement bien adaptée à ce rôle, mais elle se veut être un système universel et donc utilisable également sur un ordinateur personnel. Debian est utilisée comme base pour de nombreuses autres distributions, comme Mint ou Ubuntu.

- **1994** : La distribution commerciale RedHat est créée par la société RedHat, qui est aujourd’hui le premier distributeur du système d’exploitation GNU/Linux. RedHat soutient la version communautaire Fedora et depuis peu la distribution libre CentOS.

- **1997** : L’environnement de bureau KDE est créé. Il est basé sur la bibliothèque de composants Qt et sur le langage de développement C++.

- **1999** : L’environnement de bureau Gnome est créé. Il est quant à lui basé sur la bibliothèque de composants GTK+.

- **2002** : La distribution Arch est créée. Sa particularité est d’être diffusée en Rolling Release (mise à jour en continue).

- **2004** : Ubuntu est créée par la société Canonical (Mark Shuttleworth). Elle se base sur Debian, mais regroupe des logiciels libres et privateurs.

### Architecture
- Le noyau (ou kernel) est le premier composant logiciel.

    - Il est le cœur du système UNIX.

    - C’est lui qui gère les ressources matérielles du système.

    - Les autres composants logiciels passent obligatoirement par lui pour accéder au matériel.

- Le Shell est un utilitaire qui interprète les commandes de l’utilisateur et assure leur exécution.

    - Principaux shell : Bourne shell, C shell, Korn shell et Bourne-Again shell (bash).

- Les applications regroupent les programmes utilisateurs comme :

    - le navigateur internet ;

    - le traitement de texte ;

    - …

**_Multitâche_**
Linux fait partie de la famille des systèmes d’exploitation à temps partagé. Il partage le temps d’utilisation processus entre plusieurs programmes, passant de l’un à l’autre de façon transparente pour l’utilisateur. Cela implique :

- exécution simultanée de plusieurs programmes ;
 
- distribution du temps CPU par l’ordonnanceur ;

- réduction des problèmes dus à une application défaillante ;
 
- diminution des performances lorsqu’il y a trop de programmes lancés.

**_Multiutilisateurs_**
La finalité de Multics était de permettre à plusieurs utilisateurs de travailler à partir de plusieurs terminaux (écran et clavier) sur un seul ordinateur (très coûteux à l’époque). Linux, qui inspire de ce système d’exploitation, a gardé cette capacité à pouvoir fonctionner avec plusieurs utilisateurs simultanément et en toute indépendance, chacun ayant son compte utilisateur, son espace de mémoire et ses droits d’accès aux fichiers et aux logiciels.

**_Multiprocesseur_**
Linux est capable de travailler avec des ordinateurs multiprocesseurs ou avec des processeurs multicœurs.

**_Multiplateforme_**
Linux est écrit en langage de haut niveau pouvant s’adapter à différents types de plate-formes lors de la compilation. Il fonctionne donc sur :

- les ordinateurs des particuliers (le PC ou l’ordinateur portable) ;

- les serveurs (données, applications,…) ;

- les ordinateurs portables (les smartphones ou les tablettes) ;

- les systèmes embarqués (ordinateur de voiture) ;

- les éléments actifs des réseaux (routeurs, commutateurs) ;

- les appareils ménagers (téléviseurs, réfrigérateurs,…).
### La philosophie UNIX
- Tout est fichier.

- Portabilité.

- Ne faire qu’une seule chose et la faire bien.

- KISS : Keep It Simple and Stupid.

- “UNIX est simple, il faut juste être un génie pour comprendre sa simplicité” (Dennis Ritchie)

- “UNIX est convivial. Cependant UNIX ne précise pas vraiment avec qui.” (Steven King)

## Les domaines d’emploi
Une distribution Linux excelle pour :

- Un serveur : HTTP, messagerie, groupware, partage de fichiers, etc.

- La sécurité : Passerelle, pare-feu, routeur, proxy, etc.

- Ordinateur central : Banques, assurances, industrie, etc.

- Système embarqué : Routeurs, Box Internet, SmartTV, etc.

Linux est un choix adapté pour l’hébergement de bases de données ou de sites Web, ou comme serveur de messagerie, DNS, pare-feu. Bref Linux peut à peu près tout faire, ce qui explique la quantité de distributions spécifiques.

## Shell
### Généralités
Le shell, interface de commandes en français, permet aux utilisateurs d’envoyer des ordres au système d’exploitation. Il est moins visible aujourd’hui, depuis la mise en place des interfaces graphiques, mais reste un moyen privilégié sur les systèmes Linux qui ne possèdent pas tous des interfaces graphiques et dont les services ne possèdent pas toujours une interface de réglage.

Il offre un véritable langage de programmation comprenant les structures classiques : boucles, alternatives et les constituants courants : variables, passage de paramètres, sous-programmes. Il permet donc la création de scripts pour automatiser certaines actions (sauvegardes, création d’utilisateurs, surveillance du système,…).

Il existe plusieurs types de Shell disponibles et configurables sur une plate-forme ou selon le choix préférentiel de l’utilisateur :

- sh, le shell aux normes POSIX ;

- csh, shell orienté commandes en C ;

- bash, Bourne-Again Shell, shell de Linux.

- etc, …​