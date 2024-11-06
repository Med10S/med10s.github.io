---
title: "les bases de cryptage"
author: Issam
categories: [cybersécurité,cryptage]
tags: [cybersécurité,cryptage]
render_with_liquid: false
img_path: /images/crypto/
comments: true

image:
  path:  /images/crypto/R.png
---

---



## **1. Hachage**
Le hachage prend un texte en entrée et génère une séquence de longueur fixe (haché) qui représente ce texte. Les fonctions de hachage sont principalement utilisées pour stocker les mots de passe ou vérifier l'intégrité des données. Une fois haché, le texte est **non réversible**.

### Fonction de Hachage (ex. SHA-256) :
- **Texte en clair** : "motdepasse"
- **Hash SHA-256** : `5e884898da28047151d0e56f8dc6292773603d0d6aabbddc7d1b1a927cbd4ac4`

Le résultat est un identifiant unique de ce mot de passe. Si un autre utilisateur entre "motdepasse", il produira le même haché, ce qui permet de vérifier l’identité sans stocker le mot de passe en clair.

#### Exemple d'utilisation :
- Les bases de données de mots de passe utilisent le hachage pour ne pas enregistrer le mot de passe en clair. Lorsqu'un utilisateur entre son mot de passe, il est haché et comparé au haché stocké.

### Hachage avec Salt
Pour éviter les attaques par tables arc-en-ciel, un *sel* (valeur unique) est ajouté avant le hachage. Cela rend chaque haché unique même pour des mots de passe identiques.

---
## **2. Chiffrement**

### **1. Chiffrement Symétrique**
Le chiffrement symétrique utilise **une seule clé** pour le chiffrement et le déchiffrement. La sécurité repose sur la confidentialité de la clé. L'algorithme AES (Advanced Encryption Standard) est un des plus courants pour le chiffrement symétrique.
####  Exemple avec Chiffrement de César (Chiffrement par décalage)
Le chiffrement de César consiste à décaler chaque lettre d’un texte en clair d'un certain nombre de positions dans l’alphabet. C'est un type de chiffrement par substitution.

#### Principe :
- Chaque lettre du texte en clair est remplacée par une lettre à une distance fixe, appelée *clé de décalage*.
- Le déchiffrement s'effectue en décalant chaque lettre dans le sens inverse.

#### Exemple avec une clé de décalage de 3 :
- **Texte en clair** : "BONJOUR"
- **Clé de décalage** : 3
- **Texte chiffré** : "ERQMRXU"

**Explication** :
1. Décalage de chaque lettre :
   - B → E
   - O → R
   - N → Q
   - J → M
   - O → R
   - U → X
   - R → U
2. Résultat : "ERQMRXU"

##### Déchiffrement :
Pour déchiffrer, on effectue un décalage inverse (vers la gauche de 3 positions), récupérant ainsi le texte en clair original.

---
#### Exemple avec AES (128 bits) :
Supposons que nous voulons chiffrer le message "Message secret".

- **Clé** : `1234567890123456` (une clé de 16 caractères pour AES-128)
- **Texte en clair** : "Message secret"
- **Texte chiffré** : Après application de l'AES avec la clé, on obtient une séquence de données chiffrées, par exemple `7d58d32a8bbd5e8e1f33b3c9f40e7a6a`.

#### Déchiffrement :
Pour déchiffrer, on applique l'algorithme AES en mode inverse avec la **même clé** `1234567890123456`, et on obtient "Message secret".

#### Utilisation et Sécurité :
Le chiffrement symétrique est utilisé pour des transferts de données sécurisés, par exemple pour des connexions HTTPS, VPN, ou stockage de données locales. Cependant, il est essentiel de partager la clé en toute sécurité.

---

### **2. Chiffrement Asymétrique**
Le chiffrement asymétrique utilise une **paire de clés** : une clé publique et une clé privée. La clé publique est utilisée pour chiffrer les données, et seule la clé privée associée peut déchiffrer ces données. Cela permet des échanges sécurisés, sans partager une clé secrète unique.

#### Exemple avec RSA (un algorithme de chiffrement asymétrique) :
Supposons que l’on souhaite envoyer un message chiffré à une personne.

1. La personne génère une paire de clés (publique et privée).
2. **Texte en clair** : "Données sensibles"
3. **Chiffrement** : En utilisant la clé publique de la personne, le message est chiffré en une séquence complexe, comme `3a1f98bd8e9f4cb2...`.
4. La personne peut ensuite **déchiffrer** ce message en utilisant sa clé privée pour obtenir "Données sensibles".

#### Avantages et Utilisation :
- **Confidentialité** : Seul le détenteur de la clé privée peut déchiffrer le message.
- Utilisé pour le partage de clés dans les connexions sécurisées (SSL/TLS), les signatures numériques et les échanges sécurisés par e-mail (ex. PGP).

###  **3. Comparaison des Différents Types de Chiffrement**

| Type               | Clé                      | Exemple Utilisation                          | Avantages                                 | Inconvénients                           |
|--------------------|--------------------------|----------------------------------------------|-------------------------------------------|-----------------------------------------|
| **César**          | Clé de décalage simple   | Jeux et puzzles                              | Simple à implémenter                      | Très faible sécurité                    |
| **Hachage**        | Aucun (non réversible)   | Stockage de mots de passe, vérification      | Non réversible, unique pour chaque entrée | Non réversible                          |
| **Symétrique**     | Une clé secrète          | VPN, HTTPS, stockage de fichiers             | Rapide, efficace                          | Partage de clé difficile               |
| **Asymétrique**    | Clés publique/privée     | SSL/TLS, e-mail sécurisé, signatures         | Échange de clés sécurisé, confidentialité | Plus lent que le chiffrement symétrique |

---

Ces bases de cryptage sont essentielles dans de nombreux domaines, notamment la sécurité des réseaux, les communications chiffrées, et le stockage sécurisé. Chacun de ces types a des cas d’utilisation spécifiques et est choisi en fonction des besoins de sécurité, de vitesse et de simplicité.

---

## **3. Encodages**
Les encodages en Base64 et Base32 sont des méthodes de codage qui transforment les données binaires en texte lisible (sous forme de caractères ASCII). Ils sont souvent utilisés pour transmettre des données de manière sûre et compatible sur des canaux qui ne supportent pas les données binaires, comme les e-mails ou les URL.

### **1. Base64**
L’encodage Base64 est une méthode pour encoder des données binaires en une chaîne de texte ASCII, en utilisant 64 caractères différents. 

#### Principe
- Base64 prend trois octets (24 bits) de données binaires et les divise en quatre groupes de 6 bits.
- Chaque groupe de 6 bits est ensuite traduit en un caractère ASCII parmi les 64 disponibles : [A-Z, a-z, 0-9, +, /].
- Si le nombre total d'octets n'est pas un multiple de 3, le Base64 utilise des caractères de remplissage (`=`) pour compléter les données.

#### Exemple
- **Données en clair** : "Bonjour"
- **Données binaires** (représentation des caractères) : 01000010 01101111 01101110 01101010 01101111 01110101 01110010
- **Encodage Base64** : "Qm9uam91cg=="

Les deux caractères `==` sont ajoutés pour compléter la longueur des bits à un multiple de 24.

#### Utilisation
- Le Base64 est utilisé dans les e-mails (MIME), les certificats SSL, les images en ligne (Data URI), et les jetons JSON Web Tokens (JWT).

---

### **2. Base32**
Base32 utilise 32 caractères différents pour encoder les données binaires. Cette méthode est souvent utilisée pour l’encodage de clés dans les systèmes d’authentification (par exemple, dans le protocole TOTP pour l’authentification à deux facteurs).

#### Principe
- Base32 prend des groupes de 5 bits et les encode en utilisant 32 caractères : [A-Z, 2-7].
- Les caractères en Base32 sont conçus pour éviter toute confusion visuelle (par exemple, en excluant des caractères similaires comme `O` et `0`).

#### Exemple
- **Données en clair** : "Bonjour"
- **Encodage Base32** : "IJQXGZJAON2WS==="

#### Utilisation
- Utilisé dans les systèmes d’authentification pour stocker les clés de manière sûre.
- Également utilisé pour encoder des chaînes dans des systèmes où les erreurs de transcription doivent être minimisées (comme les codes d’invitation).

---

### **3. Base16 (Hexadécimal)**
L’encodage Base16 est aussi connu sous le nom de "hexadécimal". Il utilise 16 caractères (0-9, A-F) pour représenter chaque octet en tant que deux caractères hexadécimaux.

#### Principe
- Chaque octet (8 bits) est divisé en deux groupes de 4 bits.
- Chaque groupe de 4 bits est converti en un caractère hexadécimal.
  
#### Exemple
- **Données en clair** : "Bonjour"
- **Encodage hexadécimal** : "426F6E6A6F7572"

#### Utilisation
- Souvent utilisé pour représenter les adresses MAC, les clés de cryptage, ou les hash dans les systèmes de fichiers et les bases de données.

---

### **4. Comparaison des Encodages : Base64, Base32, Base16**

| Encodage | Caractères Utilisés          | Taille de sortie (env.)      | Usage commun                                          |
|----------|-------------------------------|------------------------------|-------------------------------------------------------|
| **Base64** | A-Z, a-z, 0-9, +, /          | 4/3 de la taille d'entrée    | Fichiers binaires dans les e-mails, certificats SSL, JWT |
| **Base32** | A-Z, 2-7                      | 8/5 de la taille d'entrée    | Clés d’authentification, TOTP, codage d'erreurs       |
| **Base16** | 0-9, A-F                      | 2x la taille d'entrée        | Représentation des adresses MAC, hash, codage de couleurs |

---

### Pourquoi Utiliser des Encodages Comme Base64 et Base32 ?
1. **Compatibilité avec les Protocoles** : Certains protocoles ou formats (comme le HTML ou l’e-mail MIME) ne supportent pas bien les données binaires, mais acceptent les caractères ASCII. Base64 et Base32 permettent donc de transmettre des données binaires de manière fiable.
  
2. **Sécurité** : En encodant des données binaires en texte lisible, on évite les erreurs de transcription et les pertes d'information lors de la transmission.

3. **Conservation de l’Intégrité des Données** : Les encodages comme Base64 sont conçus pour garantir que les données originales peuvent être restaurées de manière exacte.

---

Ces encodages ne sont pas des méthodes de chiffrement ou de protection des données ; ils servent uniquement à la **transformation de données binaires** en texte compatible ASCII.
