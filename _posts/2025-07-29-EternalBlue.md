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


# Introduction à EternalBlue

**EternalBlue** est une vulnérabilité critique découverte dans le protocole SMBv1 de Microsoft Windows. Exploitée initialement par la NSA puis rendue publique en 2017, elle permet à un attaquant de prendre le contrôle total d’un système à distance, sans authentification préalable. Cette faille a été utilisée dans des cyberattaques majeures, notamment le ransomware WannaCry, causant des dégâts mondiaux dans les entreprises et administrations.

EternalBlue repose sur un dépassement de tampon (buffer overflow) dans le service SMB, permettant l’exécution de code arbitraire au niveau du noyau Windows. Sa puissance et sa simplicité d’exploitation en font l’une des vulnérabilités les plus célèbres et redoutées de l’histoire de la cybersécurité.

---


# 📖 Guide Complet et Illustré de l'Exploit EternalBlue

Ce document détaille le fonctionnement de l'exploit EternalBlue, depuis les concepts fondamentaux jusqu'à l'exécution de code à distance, en s'appuyant sur l'implémentation de Metasploit.

---

## **Partie 1 : Les Concepts Fondamentaux**

Pour comprendre l'attaque, il faut maîtriser trois concepts clés.

### **1. Le Buffer Overflow (Dépassement de Tampon)**

C'est le cœur de la vulnérabilité.
- **Analogie simple** : C'est comme remplir un verre d'eau (le buffer) au-delà de sa capacité. L'eau déborde et se répand sur la table à côté (la mémoire voisine).
- **En informatique** : Un attaquant envoie plus de données qu'un programme n'en attend. Ces données en surplus "débordent" de l'espace mémoire alloué (le buffer) et écrasent les données stockées dans la mémoire adjacente.

### **2. La Corruption de Mémoire et le Détournement d'Exécution**

C'est la conséquence dangereuse du buffer overflow.
- La mémoire voisine qui est écrasée n'est pas anodine. Elle peut contenir des "notes" critiques pour le programme, comme des **pointeurs de fonction** (des adresses indiquant la prochaine instruction à exécuter).
- En contrôlant ce qui est écrit, l'attaquant peut remplacer l'adresse d'une fonction légitime par l'adresse de son propre code malveillant (le **shellcode**).
- Lorsque le programme suivra cette "note" corrompue, le processeur exécutera le code de l'attaquant, pensant suivre une procédure normale.

### **3. Le Pool Grooming (Préparation de la Mémoire)**

C'est la technique utilisée par l'attaquant pour garantir le succès de son attaque. Le noyau Windows alloue la mémoire dans une zone appelée le "Non-Paged Pool". L'attaquant ne peut pas laisser le hasard décider de la disposition des blocs mémoire.
- **L'objectif** : Forcer le système à placer un bloc mémoire contrôlé (la future victime de la corruption) juste à côté du bloc qui sera créé par l'opération vulnérable. C'est comme un agent immobilier qui s'assure que deux maisons spécifiques sont construites l'une à côté de l'autre.

---

## **Partie 2 : La Cible - Qu'est-ce que la structure `SRVNET_BUFFER` ?**

C'est la victime précise et technique de l'overflow.

### **Qu'est-ce que c'est ?**

Quand le noyau Windows gère une connexion réseau pour le service SMB, il ne se contente pas de stocker les données brutes. Il crée un **"dossier de gestion"** complet pour cette connexion. Ce dossier est une structure de données complexe appelée **`SRVNET_BUFFER`**.

Cette structure est toujours placée **au début du bloc de mémoire** alloué pour la connexion. C'est pourquoi on peut la simplifier en l'appelant "l'en-tête" du bloc.

### **Son Architecture et Pourquoi elle est la Cible Idéale**

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

Voici comment Metasploit combine ces concepts.

### **Étape 1 : Le "Pool Grooming" - Préparer le Terrain**

L'attaquant prépare la mémoire du noyau pour que tout s'aligne parfaitement.

**A. Placer les futures "victimes" (contenant le payload)**
L'attaquant envoie de nombreux paquets SMBv2 simples. Leur but est de forcer Windows à allouer de nombreux blocs mémoire. Chacun de ces blocs commence par une structure `SRVNET_BUFFER` et contient le payload de l'attaquant.

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

### **Étape 2 : L'Overflow et la Corruption de la `SRVNET_BUFFER` Voisine**

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
                                                   +-----------------------------------+
```

### **Étape 3 : Le Payload - La Poupée Russe**

Le payload est une "poupée russe" assemblée par `make_kernel_user_payload`.

**Poupée 1 : Le Shellcode Noyau (Kernel Mode)**
C'est le code qui est exécuté lorsque le pointeur corrompu est utilisé. Son seul but est d'injecter la seconde poupée.
- **Son travail** : Parcourir les processus, trouver `spoolsv.exe`, y allouer de la mémoire, et y copier la Poupée 2.

**Poupée 2 : Le Payload Final (User Mode - Le Reverse Shell)**
C'est le code du reverse shell (`ring3`) qui est ajouté à la fin du shellcode noyau. Une fois injecté dans `spoolsv.exe`, il s'exécute et se connecte à l'attaquant.

```ruby
# modules/exploits/windows/smb/ms17_010_eternalblue.rb
def make_kernel_user_payload(ring3, proc_name)
  proc_hash = generate_process_hash(proc_name)
  # Poupée 1 : Le shellcode Noyau, un injecteur.
  sc = (
    # ... code assembleur pour trouver un processus par son hash et y injecter du code ...
  )
  # Poupée 2 : Le payload final (reverse shell) est ajouté à la fin.
  sc << ring3
  sc
end
```

Lorsque Windows tente de traiter des données sur la connexion corrompue, il lit le pointeur de fonction modifié, saute au shellcode noyau, qui à son tour injecte et lance le reverse shell. L'attaque est un succès.

---
*Dernière mise à jour par Sbihi_Mohammed le 2025-07-29 18:02:26 UTC*