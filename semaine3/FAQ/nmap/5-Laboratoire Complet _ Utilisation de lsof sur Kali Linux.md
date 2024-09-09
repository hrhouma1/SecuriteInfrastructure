### Laboratoire Complet : Utilisation de `lsof` sur Kali Linux

#### Objectif
Fournir une série d'exercices pratiques pour apprendre à utiliser `lsof` pour voir tous les ports ouverts et gérer les processus, y compris les tuer, sur une machine Kali Linux.

### Préparation

1. **Vérifiez que `lsof` est installé sur votre machine Kali Linux :**
   ```bash
   sudo apt update
   sudo apt install lsof
   ```

### Étape 1 : Visualiser les Ports Ouverts

1. **Lister tous les fichiers ouverts par les processus :**
   ```bash
   lsof
   ```

2. **Lister tous les ports ouverts :**
   ```bash
   sudo lsof -i
   ```

3. **Lister les ports TCP ouverts :**
   ```bash
   sudo lsof -i TCP
   ```

4. **Lister les ports UDP ouverts :**
   ```bash
   sudo lsof -i UDP
   ```

5. **Lister les ports ouverts sur une plage spécifique (par exemple, 80) :**
   ```bash
   sudo lsof -i :80
   ```

### Étape 2 : Filtrer par Processus

1. **Lister les fichiers ouverts par un utilisateur spécifique (par exemple, root) :**
   ```bash
   sudo lsof -u root
   ```

2. **Lister les fichiers ouverts par un processus spécifique (par exemple, PID 1234) :**
   ```bash
   sudo lsof -p 1234
   ```

3. **Lister les fichiers ouverts par un programme spécifique (par exemple, nginx) :**
   ```bash
   sudo lsof -c nginx
   ```

### Étape 3 : Identifier et Tuer des Processus

1. **Identifier les processus utilisant un port spécifique (par exemple, 80) :**
   ```bash
   sudo lsof -i :80
   ```

2. **Tuer un processus spécifique par son PID (par exemple, PID 1234) :**
   ```bash
   sudo kill -9 1234
   ```

### Étape 4 : Exercices Pratiques

1. **Lister tous les ports ouverts sur la machine :**
   ```bash
   sudo lsof -i
   ```

2. **Lister les ports TCP ouverts :**
   ```bash
   sudo lsof -i TCP
   ```

3. **Lister les ports ouverts sur le port 80 :**
   ```bash
   sudo lsof -i :80
   ```

4. **Lister les fichiers ouverts par le processus avec PID 1234 :**
   ```bash
   sudo lsof -p 1234
   ```

5. **Identifier les processus utilisant le port 80 :**
   ```bash
   sudo lsof -i :80
   ```

6. **Tuer le processus utilisant le port 80 (remplacez `PID` par l'identifiant de processus correct) :**
   ```bash
   sudo kill -9 PID
   ```

### Résumé des Commandes

- **Lister tous les fichiers ouverts :** `lsof`
- **Lister tous les ports ouverts :** `sudo lsof -i`
- **Lister les ports TCP ouverts :** `sudo lsof -i TCP`
- **Lister les ports UDP ouverts :** `sudo lsof -i UDP`
- **Lister les ports ouverts sur le port 80 :** `sudo lsof -i :80`
- **Lister les fichiers ouverts par un utilisateur spécifique :** `sudo lsof -u root`
- **Lister les fichiers ouverts par un processus spécifique :** `sudo lsof -p 1234`
- **Lister les fichiers ouverts par un programme spécifique :** `sudo lsof -c nginx`
- **Identifier les processus utilisant le port 80 :** `sudo lsof -i :80`
- **Tuer un processus par son PID :** `sudo kill -9 1234`

### Exemple d'Exercice Pratique : Utilisation de `lsof`

1. **Lister tous les ports ouverts sur la machine :**
   ```bash
   sudo lsof -i
   ```

2. **Lister les ports TCP ouverts :**
   ```bash
   sudo lsof -i TCP
   ```

3. **Lister les ports ouverts sur le port 80 :**
   ```bash
   sudo lsof -i :80
   ```

4. **Lister les fichiers ouverts par le processus avec PID 1234 :**
   ```bash
   sudo lsof -p 1234
   ```

5. **Identifier les processus utilisant le port 80 :**
   ```bash
   sudo lsof -i :80
   ```

6. **Tuer le processus utilisant le port 80 (remplacez `PID` par l'identifiant de processus correct) :**
   ```bash
   sudo kill -9 PID
   ```

Ces exercices vous permettront de vous familiariser avec les différentes fonctionnalités de `lsof` et de comprendre comment elles peuvent être utilisées pour analyser et gérer les processus sur une machine Kali Linux. 

# Annexe : lsof ou nmap ?


### Comparaison entre `lsof` et `nmap`

#### **`lsof` (List Open Files)**
`lsof` est un utilitaire de ligne de commande qui affiche des informations sur les fichiers ouverts par les processus en cours d'exécution sur un système. Il est particulièrement utile pour diagnostiquer les problèmes liés aux fichiers et aux sockets ouverts.

- **Utilisation principale** :
  - Visualiser les fichiers ouverts par les processus.
  - Identifier les ports ouverts par les processus.
  - Diagnostiquer les problèmes de verrouillage des fichiers.

- **Fonctionnalités** :
  - Afficher les fichiers ouverts par tous les processus ou par un processus spécifique.
  - Lister les fichiers ouverts sur un port spécifique.
  - Trouver quel processus utilise un fichier spécifique.

- **Commandes courantes** :
  - `lsof` : Affiche tous les fichiers ouverts.
  - `lsof -i :port` : Affiche les processus utilisant un port spécifique.
  - `lsof /path/to/file` : Affiche les processus utilisant un fichier spécifique.

#### **`nmap` (Network Mapper)**
`nmap` est un utilitaire de ligne de commande utilisé pour l'exploration et l'audit de la sécurité des réseaux. Il permet de scanner les réseaux pour découvrir les hôtes et les services disponibles, ainsi que pour identifier les failles potentielles.

- **Utilisation principale** :
  - Scanner les réseaux pour découvrir les hôtes et les services.
  - Identifier les ports ouverts sur les hôtes.
  - Effectuer des audits de sécurité.

- **Fonctionnalités** :
  - Scan de ports pour identifier les services en cours d'exécution.
  - Détection du système d'exploitation et des versions des services.
  - Scans furtifs pour contourner les systèmes de détection d'intrusion.
  - Détection de vulnérabilités spécifiques.

- **Commandes courantes** :
  - `nmap <ip_adresse>` : Scanner un hôte spécifique.
  - `nmap -p 1-65535 <ip_adresse>` : Scanner tous les ports sur un hôte spécifique.
  - `nmap -sS <ip_adresse>` : Effectuer un scan furtif (SYN scan).

### Comparaison et Puissance

#### Puissance et Fonctionnalité

- **`lsof`** :
  - Plus axé sur la gestion et le diagnostic des fichiers et des sockets locaux.
  - Utile pour les administrateurs système qui veulent comprendre quels fichiers sont utilisés par quels processus.
  - Limité à l'information sur le système local uniquement.

- **`nmap`** :
  - Axé sur l'exploration et la sécurité des réseaux.
  - Permet de découvrir des hôtes et des services à travers un réseau entier.
  - Plus puissant pour les audits de sécurité et l'évaluation des réseaux externes.
  - Fournit des capacités avancées pour la détection de systèmes d'exploitation, les scans furtifs et la découverte de vulnérabilités.

#### Conclusion
`nmap` est plus puissant en termes d'exploration et d'audit de sécurité des réseaux, offrant une large gamme de fonctionnalités pour découvrir et analyser les hôtes et les services sur un réseau. `lsof`, en revanche, est plus spécialisé pour le diagnostic local des fichiers et des sockets ouverts par les processus sur un système. Le choix entre les deux dépend de l'objectif : utilisation locale pour la gestion des fichiers (lsof) ou exploration et audit de sécurité du réseau (nmap).


