---
title: "EternalBlue"
author: med
categories: [cybersécurité]
tags: [EternalBlue , cybersécurité, Wind7 ]
render_with_liquid: false
math: true

img_path: /images/eternalblue/
image:
  path:  /images/eternalblue/petya1.webp
---

# 📖 Guide Complet sur l'Exploit EternalBlue

Ce document détaille le fonctionnement de l'exploit EternalBlue, depuis les concepts fondamentaux jusqu'à l'exécution de code à distance, en s'appuyant sur l'implémentation de Metasploit.  
**L'analogie du chef cuisinier est utilisée pour vulgariser, mais la dernière section adopte une approche professionnelle avec les termes techniques du domaine, en s'appuyant sur les fonctions du noyau Windows.**

---

## **Partie 1 : Les Concepts Fondamentaux (avec analogie du chef)**

### **1. Le Buffer Overflow (Dépassement de Tampon)**

#### 🧑‍🍳 **Analogie du chef**
Imaginez que la mémoire de l'ordinateur est une grande cuisine.  
Le chef (le processeur) prépare des plats dans des casseroles (les buffers).  
Si le chef verse trop de soupe dans la casserole, la soupe déborde et coule sur la table à côté (la mémoire voisine).

#### 🖥️ **Dans la réalité**
Un programme alloue un espace mémoire pour stocker des données (le buffer).  
Si un attaquant envoie plus de données que la capacité du buffer, ces données débordent et écrasent des zones mémoire voisines qui contiennent des informations importantes du système.

---

### **2. La Corruption de Mémoire et le Détournement d'Exécution**

#### 🧑‍🍳 **Analogie du chef**
À côté de chaque casserole, le chef a une "note" qui lui dit quelle recette faire ensuite.  
Si la soupe déborde et recouvre la note, le chef pourrait lire une mauvaise instruction, comme "Fais un gâteau" alors qu'il devrait faire une soupe.

#### 🖥️ **Dans la réalité**
La mémoire voisine peut contenir des pointeurs de fonction (adresses qui indiquent au processeur quelle instruction exécuter ensuite).  
Si l'attaquant remplace cette adresse par celle de son propre code, le processeur va exécuter le code malveillant de l'attaquant au lieu du code légitime.

---

### **3. Le Pool Grooming (Préparation de la Mémoire)**

#### 🧑‍🍳 **Analogie du chef**
Le chef doit s'assurer que la casserole qui va déborder est placée juste à côté de la note à modifier.  
Il organise la cuisine pour que tout soit bien aligné : la casserole (buffer vulnérable), la note (pointeur), et l'espace où il va verser la soupe (payload).

#### 🖥️ **Dans la réalité**
L'attaquant envoie des requêtes pour manipuler la façon dont le système alloue les blocs mémoire.  
Il s'assure que le buffer vulnérable sera alloué juste à côté de la structure qu'il veut corrompre (`SRVNET_BUFFER`).


---

## **Partie 2 : La Cible - Qu'est-ce que la structure `SRVNET_BUFFER` ?**

### 🧑‍🍳 **Analogie**
Chaque recette que le chef reçoit est accompagnée d'un dossier de gestion : il contient la liste des ingrédients, la taille de la casserole, et surtout une note qui lui dit quelle recette faire après.

### 🖥️ **Dans la réalité**
Quand Windows gère une connexion réseau SMB, il crée une structure complexe appelée **`SRVNET_BUFFER`** au début du bloc mémoire.  

La structure `SRVNET_BUFFER` contient toutes les informations vitales pour que Windows puisse traiter les données reçues. Son importance pour l'attaquant vient d'un de ses champs spécifiques.

```
+-------------------------------------------------------------+  <-- Début d'un bloc mémoire alloué par SMB
|                     SRVNET_BUFFER (le "dossier")            |
| +---------------------------------------------------------+ |
| | Pointeur vers les données brutes du paquet              | |
| +---------------------------------------------------------+ |
| | Taille des données                                      | |
| +---------------------------------------------------------+ |
| | Flags (Indicateurs de statut, etc.)                     | |
| +---------------------------------------------------------+ |
| | ... autres informations de gestion ...                  | |
| +---------------------------------------------------------+ |
| | POINTEUR VERS LA FONCTION DE TRAITEMENT (Handler)       | |  <-- LA CIBLE ! La fameuse "note" à écraser.
| +---------------------------------------------------------+ |
|                                                             |
+=============================================================+
|                                                             |
|                 DONNÉES BRUTES DU PAQUET                    |
|             (C'est ici que l'attaquant place                |
|                    son shellcode/payload)                   |
|                                                             |
+-------------------------------------------------------------+
```
Le champ **"Pointeur vers la fonction de traitement"** est la clé.
- **Rôle légitime** : Quand des données arrivent sur cette connexion, le système regarde ce pointeur pour savoir quelle fonction appeler pour les traiter. Normalement, il pointe vers une fonction interne et sécurisée de `srvnet.sys` (comme `SrvNetWskReceiveComplete`).
- **L'idée de l'attaque** : Si l'attaquant peut réécrire ce pointeur, il peut forcer le système à exécuter n'importe quel code de son choix.

---

## **Partie 3 : Visualisation de l'Attaque Complète (Mémoire et Code)**

### **Étape 1 : Le "Pool Grooming"**

#### 🧑‍🍳 **Analogie**
Le chef prépare la cuisine : il place des casseroles (blocs mémoire avec payload) alignées sur la table, et crée un espace vide (trou) à côté de la note qu'il veut modifier.

#### 🖥️ **Dans la réalité**
L'attaquant prépare la mémoire du noyau pour que tout s'aligne parfaitement.

**A. Placer les futures "victimes" (contenant le payload)**
L'attaquant envoie de nombreux paquets SMBv2 simples. Leur but est de forcer Windows à allouer de nombreux blocs mémoire. Chacun de ces blocs commence par une structure `SRVNET_BUFFER` et contient le payload de l'attaquant. ce n'est que de la donnée (comme une liste d'ingrédients). Le serveur, en faisant son travail, stocke cette "liste d'ingrédients" sur une page blanche à la fin du livre. Cette "liste" est en réalité le payload de l'attaquant : une recette malveillante qui dit "Donne-moi un shell". À ce stade, ce n'est que du texte inerte sur une page. Le chef ne la regarde même pas. Disons que c'est à la page 5000.

```ruby
# modules/exploits/windows/smb/ms17_010_eternalblue.rb
# La fonction smb2_grooms envoie de nombreux paquets pour remplir la mémoire.
def smb2_grooms(grooms, payload_hdr_pkt)
  grooms.times do |_groom_id|
    gsock = connect(false)
    @groom_socks << gsock
    # Chaque paquet contient le payload (créé par make_smb2_payload_body_packet)
    gsock.put(payload_hdr_pkt) 
  end
end
```
La mémoire ressemble maintenant à :
`| SRVNET_BUFFER #1 | SRVNET_BUFFER #2 | SRVNET_BUFFER #3 | ... |`

**B. Créer un "Trou"**
Via une manipulation de connexions SMBv1, l'attaquant force Windows à libérer l'un de ces blocs, créant un "trou" au milieu de ses blocs préparés.

```ruby
# modules/exploits/windows/smb/ms17_010_eternalblue.rb
fhs_sock = smb1_free_hole(true) # Cette fonction libère un bloc mémoire.
fhs_sock.shutdown              # La fermeture de la connexion finalise la libération.
```
La mémoire ressemble maintenant à ceci :
`| SRVNET_BUFFER #1 | <-- TROU LIBRE --> | SRVNET_BUFFER #3 | ... |`

---

### **Étape 2 : L'Overflow et la Corruption de la Structure Voisine**

#### 🧑‍🍳 **Analogie**
Le chef verse délibérément trop de soupe dans la casserole vulnérable, la soupe déborde sur la note voisine et la recouvre avec une nouvelle instruction écrite par l'attaquant : "Va à la page 5000 et suis la recette là-bas !"

#### 🖥️ **Dans la réalité**
L'attaquant envoie le paquet `TRANSACTION2` final, construit par `make_smb1_trans2_exploit_packet`. À cause de la faille de calcul, Windows alloue un petit buffer dans le "trou" pour y copier les données.

```
ÉTAT DE LA MÉMOIRE AU MOMENT DE L'OVERFLOW

...| SRVNET_BUFFER #2 | [Buffer Vulnérable] | SRVNET_BUFFER #3 (La Cible) | ...
```
La copie des données commence. Le paquet est bien plus grand que le "Buffer Vulnérable" et déborde, écrasant la structure `SRVNET_BUFFER` du bloc voisin (`#3`).

```ruby
# modules/exploits/windows/smb/ms17_010_eternalblue.rb
# Le paquet est construit pour que les données qui débordent contiennent les adresses malveillantes.
when :eb_trans2_exploit
  # ...
  pkt << "\x41" * 2957 # Remplissage pour atteindre la cible
  # ...
  # ADRESSE MALVEILLANTE (pour x64) qui va écraser le pointeur de fonction légitime.
  # Cette adresse pointe vers le shellcode que l'on a déjà placé dans le bloc #3.
  pkt << "\x00\xf1\xdf\xff\xff\xff\xff\xff" 
```
Le schéma de la corruption est le suivant :

```
[Buffer Vulnérable]<---- DONNÉES QUI DÉBORDENT ---->|     SRVNET_BUFFER #3 CORROMPUE      |
                                                   +-----------------------------------+
                                                   | Pointeur de Fonction (Handler)    |
                                                   | AVANT: (Adresse légitime Windows) |
                                                   | APRÈS: (Adresse de notre payload) |
                                                   +-----------------------------------+
                                                   | ... Reste du Bloc #3 ...          |
                                                   | (Contient déjà notre shellcode !) |
    

```
---
### **Étape 3 : L'Exécution du Payload**

#### 🧑‍🍳 **Analogie**
Le chef lit la note corrompue, va à la page indiquée et suit la fausse recette.

#### 🖥️ **Dans la réalité**
Windows lit le pointeur de fonction modifié, saute à l'adresse du shellcode, et exécute le code malveillant.
Le shellcode, quand il est juste stocké en mémoire, n'est qu'une suite d'octets. Pour le système, c'est de la donnée, du "texte. Il est inoffensif. Il pourrait s'agir des pixels d'une image de chat ou d'un poème.

Le "miracle" se produit à cause du **Pointeur d'Instruction du CPU** (le registre `RIP`).

1.  Le système, voulant traiter des données, suit le pointeur de fonction corrompu.
2.  Il dit au CPU : "OK, ta prochaine instruction à exécuter se trouve à l'adresse `0xFEEDBEEF` (l'adresse de notre shellcode)".
3.  Le CPU, qui est une machine bête et obéissante, déplace son Pointeur d'Instruction (`RIP`) à l'adresse `0xFEEDBEEF`.
4.  À partir de cet instant, le CPU ne considère plus les octets à cette adresse comme du "texte" ou de la "donnée". Il les interprète comme sa **prochaine commande à exécuter**.

Le premier octet du shellcode pourrait être `\x55`, ce qui pour le CPU signifie "Sauvegarde le registre de base sur la pile" (`PUSH RBP`). Le CPU l'exécute. Puis il lit l'octet suivant, et ainsi de suite.

La donnée est devenue du code exécutable **au moment précis où le CPU a reçu l'ordre de la considérer comme sa prochaine instruction**
---

### **Étape 4 : Le Payload - La Poupée Russe**

#### 🧑‍🍳 **Analogie**
La fausse recette indique au chef d'aller voir l'assistant, lui donner une casserole, et placer la vraie recette (reverse shell) dedans.

#### 🖥️ **Dans la réalité**


### Contenu de la "Page 5000" (Le Payload)
Il s'agit du contenu du payload, qui est créé par la fonction `make_kernel_user_payload` et placé dans les blocs de "grooming" par `make_smb2_payload_body_packet`.

Ce payload est une véritable "poupée russe" en deux parties :

#### Partie 1 : Le Shellcode Noyau (Kernel Mode)

C'est le code qui s'exécute en premier, avec les privilèges maximum (Niveau 0 / Ring 0). Il ne peut pas directement lancer `cmd.exe` ou un reverse shell. Son travail est de faire le pont vers le monde utilisateur (Niveau 3 / Ring 3).

Son contenu, en langage humain, est le suivant :

1.  "Mets en pause tout ce que tu fais."
2.  "Parcours la liste de tous les processus qui tournent sur le système."
3.  "Pour chaque processus, calcule un `hash` de son nom (ex: `ror13_hash('spoolsv.exe')`)."
4.  "Compare ce hash avec le hash que l'attaquant m'a donné (`proc_hash`)."
5.  "Ah ! J'ai trouvé le bon processus (`spoolsv.exe`) !"
6.  "Maintenant, utilise les fonctions du noyau pour allouer une nouvelle page de mémoire à l'intérieur de ce processus."
7.  "Copie la deuxième partie de mon code (le payload final, le reverse shell) dans cette page mémoire."
8.  "Crée un nouveau fil d'exécution (un "thread") dans le processus `spoolsv.exe` et dis-lui de commencer à exécuter le code que je viens de copier."
9.  "Mon travail est terminé, je nettoie mes traces et je rends la main."

#### Partie 2 : Le Payload Final (User Mode - Le Reverse Shell)

C'est le code que tout le monde connaît de Metasploit (ex: `windows/x64/meterpreter/reverse_tcp`). Il est ajouté à la fin du shellcode noyau par la ligne `sc << ring3`. Une fois que le shellcode noyau l'a injecté et lancé dans `spoolsv.exe`, il s'exécute comme un programme normal et fait ce pour quoi il est conçu :

1.  "Ouvre une connexion réseau vers l'adresse IP et le port de l'attaquant."
2.  "Lance un interpréteur de commandes (`cmd.exe`)."
3.  "Redirige toutes les entrées et sorties de `cmd.exe` vers la connexion réseau."
4.  "Attends les commandes de l'attaquant."

C'est à ce moment que la session s'ouvre dans Metasploit et que le hacker peut taper des commandes.

En résumé, l'exploit est une chaîne d'événements brillamment orchestrée :
1.  **Préparation** : Placer le payload (poupée russe) dans des blocs mémoire.
2.  **Corruption** : Déclencher un overflow pour remplacer une "note" (un pointeur de fonction) par l'adresse de la première partie du payload.
3.  **Exécution Noyau** : Le CPU exécute la première partie, qui agit comme un injecteur.
4.  **Exécution Utilisateur** : L'injecteur lance la seconde partie (le reverse shell) dans un processus légitime, donnant le contrôle à l'attaquant.

---

## **Partie 4 : Analyse Technique**

À présent, nous quittons l'analogie pour détailler précisément le fonctionnement technique.

### **1. Les fonctions du noyau Windows**

- **La faille se situe dans le driver `srv.sys`**, plus précisément dans la gestion des requêtes SMBv1 TRANS2 (commande 0x32).
- **La fonction vulnérable :** `SrvOs2FeaToNt` réalise une copie de la liste FEA dans un buffer alloué selon une taille calculée par `SrvOs2FeaListSizeToNt`. À cause d'une erreur arithmétique (overflow entre DWORD et WORD), la taille est sous-estimée et la mémoire allouée est trop petite.

### **2. La structure mémoire cible**

- **Le buffer vulnérable** est alloué dans le Non-Paged Pool du noyau.
- **La structure voisine** est une instance de `SRVNET_BUFFER`, qui gère une connexion SMBv2.
- **Le champ critique** est un pointeur de fonction utilisé par le driver `srvnet.sys` pour dispatcher les opérations réseau, comme `SrvNetWskReceiveComplete`.

### **3. Le déroulement de l'attaque**

1. **Pool Grooming:**  
   L'attaquant ouvre et ferme des connexions SMB pour organiser l'ordre d'allocation des blocs mémoire.
2. **Trigger de l'overflow:**  
   En envoyant un paquet TRANS2 malveillant, il force la copie de données dans un buffer trop petit, qui déborde sur la structure `SRVNET_BUFFER` voisine.
3. **Corruption du pointeur:**  
   Les octets débordants écrasent le pointeur de fonction, le remplaçant par l'adresse du shellcode placé dans le bloc de grooming.
4. **Exécution du shellcode:**  
   Lors d'une opération réseau ultérieure, Windows utilise la structure corrompue et appelle la fonction à l'adresse de l'attaquant, exécutant le shellcode en mode noyau.
5. **Injection du payload utilisateur:**  
   Le shellcode noyau injecte le payload final (reverse shell) dans un processus utilisateur (`spoolsv.exe`), permettant à l'attaquant de prendre le contrôle total du système.

### **4. Extraits de code illustratifs (Metasploit)**

```ruby
# Calcul de la taille vulnérable (SrvOs2FeaListSizeToNt)
# Le bug : DWORD - WORD => overflow
# Buffer sous-dimensionné
```

```ruby
# Construction du paquet SMB pour l'overflow :
pkt << "\x00\xf1\xdf\xff\xff\xff\xff\xff"  # Nouvelle adresse à la place du pointeur légitime
```

```ruby
# Shellcode injecté :
def make_kernel_user_payload(ring3, proc_name)
  proc_hash = generate_process_hash(proc_name)
  # Shellcode noyau (injecteur)
  sc = ( ... )
  # Reverse shell ajouté à la fin
  sc << ring3
end
```

---

## En résumé

EternalBlue est une exploitation de corruption mémoire dans le driver SMB de Windows (srv.sys), utilisant une erreur arithmétique pour provoquer un buffer overflow qui cible la structure voisine `SRVNET_BUFFER`.  
L'attaque permet l'exécution de code arbitraire en mode noyau, qui injecte un payload dans un processus utilisateur, donnant un accès SYSTEM à l'attaquant.

---
*Dernière mise à jour par imi-SB le 2025-07-29 19:18 UTC*