---
title: "Les fondamentaux de Linux 2"
author: yassine
categories: [linux]
tags: [linux]
render_with_liquid: false
img_path: /images/linux/
comments: true

image:
  path:  /images/linux/i2.png
---


## Commandes Linux courantes

### 1. **ls** (Liste les fichiers et répertoires)
- **Options :**
  - `-l` : Affichage détaillé.
  - `-a` : Affiche tous les fichiers, y compris les fichiers cachés.
  - `-h` : Affiche les tailles de fichiers de manière lisible (Ko, Mo).
  
- **Exemple :**
  ```bash
  ls -lah
  ```

### 2. **cd** (Change de répertoire)
- **Exemple :**
  ```bash
  cd /path/to/directory
  ```

### 3. **cp** (Copie des fichiers ou des répertoires)
- **Options :**
  - `-r` : Copie récursivement.
  - `-i` : Demande confirmation avant de remplacer les fichiers.
  
- **Exemple :**
  ```bash
  cp -ri source_directory destination_directory
  ```

### 4. **mv** (Déplace ou renomme des fichiers ou des répertoires)
- **Exemple :**
  ```bash
  mv oldname newname
  ```

### 5. **rm** (Supprime des fichiers ou des répertoires)
- **Options :**
  - `-r` : Supprime récursivement.
  - `-f` : Force la suppression sans confirmation.
  
- **Exemple :**
  ```bash
  rm -rf directory_to_remove
  ```

### 6. **mkdir** (Crée un nouveau répertoire)
- **Options :**
  - `-p` : Crée des répertoires parents si nécessaire.
  
- **Exemple :**
  ```bash
  mkdir -p new_directory/sub_directory
  ```

### 7. **rmdir** (Supprime un répertoire vide)
- **Exemple :**
  ```bash
  rmdir empty_directory
  ```

### 8. **touch** (Crée un fichier vide ou met à jour la date d'accès)
- **Exemple :**
  ```bash
  touch newfile.txt
  ```

### 9. **cat** (Affiche le contenu d'un fichier)
- **Options :**
  - `-n` : Numérote les lignes affichées.
  
- **Exemple :**
  ```bash
  cat -n file.txt
  ```

### 10. **echo** (Affiche un message ou une variable)
- **Exemple :**
  ```bash
  echo "Hello World"
  ```

### 11. **man** (Affiche le manuel d'une commande)
- **Exemple :**
  ```bash
  man ls
  ```

### 12. **pwd** (Affiche le répertoire de travail actuel)
- **Exemple :**
  ```bash
  pwd
  ```

### 13. **find** (Recherche des fichiers et des répertoires)
- **Options :**
  - `-name` : Recherche par nom.
  - `-type` : Spécifie le type de fichier (f pour fichier, d pour répertoire).
  
- **Exemple :**
  ```bash
  find /path/to/search -name "*.txt"
  ```

### 14. **grep** (Recherche des motifs dans les fichiers)
- **Options :**
  - `-i` : Ignore la casse.
  - `-r` : Recherche récursive.
  
- **Exemple :**
  ```bash
  grep -i "pattern" filename.txt
  ```

### 15. **chmod** (Modifie les permissions des fichiers)
- **Options :**
  - `+` : Ajoute des permissions.
  - `-` : Retire des permissions.
  
- **Exemple :**
  ```bash
  chmod +x script.sh
  ```

### 16. **chown** (Modifie le propriétaire et le groupe d'un fichier)
- **Options :**
  - `:group` : Spécifie le groupe.
  
- **Exemple :**
  ```bash
  chown user:group filename.txt
  ```

### 17. **df** (Affiche l'utilisation de l'espace disque)
- **Options :**
  - `-h` : Affiche en format lisible (humain).
  
- **Exemple :**
  ```bash
  df -h
  ```

### 18. **du** (Affiche l'utilisation de l'espace par fichier ou répertoire)
- **Options :**
  - `-h` : Format lisible.
  - `-s` : Affiche seulement le total.
  
- **Exemple :**
  ```bash
  du -sh /path/to/directory
  ```

### 19. **ps** (Affiche les processus en cours)
- **Options :**
  - `-aux` : Affiche tous les processus.
  
- **Exemple :**
  ```bash
  ps aux
  ```

### 20. **kill** (Termine des processus)
- **Options :**
  - `-9` : Force l'arrêt.
  
- **Exemple :**
  ```bash
  kill -9 PID
  ```

### 21. **top** (Affiche les processus en cours en temps réel)
- **Exemple :**
  ```bash
  top
  ```

### 22. **scp** (Copie des fichiers entre des hôtes via SSH)
- **Options :**
  - `-r` : Pour copier récursivement.
  
- **Exemple :**
  ```bash
  scp -r local_folder user@remote_host:/remote/path
  ```

### 23. **wget** (Télécharge des fichiers depuis le web)
- **Options :**
  - `-P` : Spécifie le répertoire de destination.
  
- **Exemple :**
  ```bash
  wget -P /path/to/destination https://example.com/file
  ```

### 24. **curl** (Transfère des données avec des URL)
- **Options :**
  - `-O` : Sauvegarde le fichier avec le nom d'origine.
  
- **Exemple :**
  ```bash
  curl -O https://example.com/file
  ```

### 25. **tar** (Archive des fichiers)
- **Options :**
  - `-czf` : Crée une archive compressée.
  - `-xf` : Extrait une archive.
  
- **Exemple :**
  ```bash
  tar -czf archive.tar.gz /path/to/folder
  ```

### 26. **history** (Affiche l'historique des commandes)
- **Exemple :**
  ```bash
  history
  ```

### 27. **clear** (Efface l'écran du terminal)
- **Exemple :**
  ```bash
  clear
  ```

### 28. **alias** (Crée un alias pour une commande)
- **Exemple :**
  ```bash
  alias ll='ls -lah'
  ```

### 29. **ping** (Teste la connectivité réseau)
- **Options :**
  - `-c` : Spécifie le nombre de paquets à envoyer.
  
- **Exemple :**
  ```bash
  ping -c 4 google.com
  ```

### 30. **traceroute** (Affiche le chemin emprunté par les paquets vers un hôte)
- **Exemple :**
  ```bash
  traceroute google.com
  ```

### 31. **ifconfig** (Affiche ou configure les interfaces réseau)
- **Exemple :**
  ```bash
  ifconfig
  ```

### 32. **netstat** (Affiche les connexions réseau, les tables de routage, etc.)
- **Options :**
  - `-t` : Affiche les connexions TCP.
  - `-u` : Affiche les connexions UDP.
  
- **Exemple :**
  ```bash
  netstat -tuln
  ```

### 33. **service** (Gère les services du système)
- **Exemple :**
  ```bash
  service apache2 start
  ```

### 34. **systemctl** (Gère les services et les unités)
- **Options :**
  - `start` : Démarre un service.
  - `stop` : Arrête un service.
  
- **Exemple :**
  ```bash
  systemctl start apache2
  ```

### 35. **nano** (Éditeur de texte en ligne de commande)
- **Exemple :**
  ```bash
  nano filename.txt
  ```

### 36. **vim** (Éditeur de texte avancé en ligne de commande)
- **Exemple :**
  ```bash
  vim filename.txt
  ```

### 37. **chmod** (Change les permissions d'un fichier)
- **Options :**
  - `u` : Utilisateur.
  - `g` : Groupe.
  - `o` : Autres.
  
- **Exemple :**
  ```bash
  chmod u+x script.sh
  ```

### 38. **df** (Affiche l'utilisation de l'espace disque)
- **Options :**
  - `-h` : Affichage lisible par l'homme.
  
- **Exemple :**
  ```bash
  df -h
  ```

### 39. **du** (Affiche la taille des fichiers et répertoires)
- **Options :**
  - `-sh` : Affiche seulement la taille totale.
  
- **Exemple :**
  ```bash
  du -sh /path/to/directory
  ```

### 40. **wget** (Télécharge des fichiers depuis Internet)
- **Options :**
  - `-q` : Mode silencieux (ne montre pas les détails).
  
- **Exemple :**
  ```bash
  wget -q https://example.com/file.txt
  ```


### 41. **basename** (Affiche le nom de fichier sans le chemin)
- **Exemple :**
  ```bash
  basename /path/to/file.txt
  ```

### 42. **dirname** (Affiche le chemin du répertoire d'un fichier)
- **Exemple :**
  ```bash
  dirname /path/to/file.txt
  ```

### 43. **uname** (Affiche des informations sur le système)
- **Options :**
  - `-a` : Affiche toutes les informations.
  
- **Exemple :**
  ```bash
  uname -a
  ```

### 44. **date** (Affiche ou définit la date et l'heure)
- **Exemple :**
  ```bash
  date '+%Y-%m-%d %H:%M:%S'
  ```

---

