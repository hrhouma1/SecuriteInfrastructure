### Laboratoire Complet : Utilisation de Nmap et lsof sur un Réseau Local avec Kali Linux

#### Objectif
- Apprendre à utiliser Nmap et lsof afin de scanner les ports, détecter les services et les systèmes d'exploitation, utiliser des scripts de vulnérabilité et gérer les processus sur une machine Kali Linux.

### Préparation

1. **Vérifiez que Nmap et lsof sont installés sur votre machine Kali Linux :**
   ```bash
   sudo apt update
   sudo apt install nmap lsof
   ```

2. **Assurez-vous que votre serveur local et les autres machines sont connectés au même réseau NAT.**

### Partie 1 : Utilisation de Nmap

#### Étape 1 : Scanner les Machines sur le Réseau Local

1. **Scanner tous les hôtes connectés au même réseau :**
   ```bash
   nmap -sn 192.168.1.0/24
   ```

2. **Scanner `localhost` :**
   ```bash
   nmap localhost
   ```

#### Étape 2 : Scans SYN sur le Réseau Local

1. **Scan SYN pour détecter les ports ouverts sur tous les hôtes :**
   ```bash
   nmap -sS 192.168.1.0/24
   ```

2. **Scan SYN pour `localhost` :**
   ```bash
   nmap -sS localhost
   ```

#### Étape 3 : Détection des Services et Versions sur le Réseau Local

1. **Détection des services et versions sur les ports ouverts :**
   ```bash
   nmap -sV 192.168.1.0/24
   ```

2. **Détection des services et versions sur `localhost` :**
   ```bash
   nmap -sV localhost
   ```

#### Étape 4 : Détection du Système d'Exploitation sur le Réseau Local

1. **Détection du système d'exploitation des machines sur le réseau :**
   ```bash
   nmap -O 192.168.1.0/24
   ```

2. **Détection du système d'exploitation de `localhost` :**
   ```bash
   nmap -O localhost
   ```

#### Étape 5 : Scan Complet avec Détection d'OS et de Services

1. **Scan complet incluant la détection d'OS et des services sur tout le réseau :**
   ```bash
   nmap -A 192.168.1.0/24
   ```

2. **Scan complet incluant la détection d'OS et des services sur `localhost` :**
   ```bash
   nmap -A localhost
   ```

#### Étape 6 : Utilisation des Scripts Nmap pour la Détection des Vulnérabilités

1. **Utiliser les scripts Nmap pour vérifier les vulnérabilités courantes sur le réseau :**
   ```bash
   nmap --script vuln 192.168.1.0/24
   ```

2. **Utiliser les scripts Nmap pour vérifier les vulnérabilités courantes sur `localhost` :**
   ```bash
   nmap --script vuln localhost
   ```

#### Étape 7 : Scans Avancés sur le Réseau Local

1. **Scan UDP pour détecter les services UDP :**
   ```bash
   nmap -sU 192.168.1.0/24
   ```

2. **Scan de connexion TCP (plus lent mais plus complet) :**
   ```bash
   nmap -sT 192.168.1.0/24
   ```

3. **Scan de fenêtre TCP :**
   ```bash
   nmap -sW 192.168.1.0/24
   ```

4. **Scan de ACK TCP :**
   ```bash
   nmap -sA 192.168.1.0/24
   ```

#### Étape 8 : Évasion de Pare-feu et Usurpation

1. **Utiliser des leurres pour masquer l'origine du scan :**
   ```bash
   nmap -D RND:10 192.168.1.0/24
   ```

2. **Fragmenter les paquets pour contourner les pare-feu :**
   ```bash
   nmap -f 192.168.1.0/24
   ```

3. **Changer les options de timing pour éviter la détection :**
   ```bash
   nmap -T4 192.168.1.0/24
   ```

#### Étape 9 : Enregistrement des Résultats

1. **Enregistrer les résultats en format texte normal :**
   ```bash
   nmap -oN resultat.txt 192.168.1.0/24
   ```

2. **Enregistrer les résultats en format XML :**
   ```bash
   nmap -oX resultat.xml 192.168.1.0/24
   ```

3. **Enregistrer les résultats en format Grepable :**
   ```bash
   nmap -oG resultat.gnmap 192.168.1.0/24
   ```

### Partie 2 : Utilisation de lsof

#### Étape 1 : Visualiser les Ports Ouverts

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

#### Étape 2 : Filtrer par Processus

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

#### Étape 3 : Identifier et Tuer des Processus

1. **Identifier les processus utilisant un port spécifique (par exemple, 80) :**
   ```bash
   sudo lsof -i :80
   ```

2. **Tuer un processus spécifique par son PID (par exemple, PID 1234) :**
   ```bash
   sudo kill -9 1234
   ```

### Résumé des Commandes

#### Nmap

- **Scan de base sur le réseau :** `nmap -sn 192.168.1.0/24`
- **Scan de base sur `localhost` :** `nmap localhost`
- **Scan SYN sur le réseau :** `nmap -sS 192.168.1.0/24`
- **Scan SYN sur `localhost` :** `nmap -sS localhost`
- **Détection des services et versions sur le réseau :** `nmap -sV 192.168.1.0/24`
- **Détection des services et versions sur `localhost` :** `nmap -sV localhost`
- **Détection du système d'exploitation sur le réseau :** `nmap -O 192.168.1.0/24`
- **Détection du système d'exploitation sur `localhost` :** `nmap -O localhost`
- **Scan complet sur le réseau :** `nmap -A 192.168.1.0/24`
- **Scan complet sur `localhost` :** `nmap -A localhost`
- **Détection des vulnérabilités sur le réseau :** `nmap --script vuln 192.168.1.0/24`
- **Détection des vulnérabilités sur `localhost` :** `nmap --script vuln localhost`
- **Scan UDP sur le réseau :** `nmap -sU 192.168.1.0/24`
- **Scan de connexion TCP sur le réseau :** `nmap -sT 192.168.1.0/24`
- **Scan de fenêtre TCP sur le réseau :** `nmap -sW 192.168.1.0/24`
- **Scan de ACK TCP sur le réseau :** `nmap -sA 192.168.1.0/24`
- **Leurres sur le réseau :** `nmap -D RND:10 192.168.1.0/24`
- **Fragmentation sur le réseau :** `nmap -f 192.168.1.0/24`
- **Options de timing sur le réseau :** `nmap -T4 192.168.1.0/24`
- **Enregistrer en texte normal :** `nmap -oN resultat.txt 192.168.1.0/24`
- **Enregistrer en XML :** `nmap -oX resultat.xml 192.168.1.0/24`
- **Enregistrer en Grepable

 :** `nmap -oG resultat.gnmap 192.168.1.0/24`

#### lsof

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

### Exemples d'Exercices Pratiques

#### Exercice 1 : Scan de Réseau avec Nmap

1. **Scanner tous les hôtes connectés au réseau :**
   ```bash
   nmap -sn 192.168.1.0/24
   ```

2. **Scan SYN des hôtes :**
   ```bash
   nmap -sS 192.168.1.0/24
   ```

3. **Détection des services et versions :**
   ```bash
   nmap -sV 192.168.1.0/24
   ```

4. **Détection du système d'exploitation :**
   ```bash
   nmap -O 192.168.1.0/24
   ```

5. **Scan complet incluant OS et services :**
   ```bash
   nmap -A 192.168.1.0/24
   ```

6. **Détection des vulnérabilités :**
   ```bash
   nmap --script vuln 192.168.1.0/24
   ```

7. **Enregistrer les résultats en texte normal :**
   ```bash
   nmap -oN resultat.txt 192.168.1.0/24
   ```

8. **Enregistrer les résultats en XML :**
   ```bash
   nmap -oX resultat.xml 192.168.1.0/24
   ```

#### Exercice 2 : Utilisation de lsof

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

Ces exercices vous permettront de vous familiariser avec les différentes fonctionnalités de Nmap et lsof et de comprendre comment elles peuvent être utilisées pour analyser et gérer les systèmes sur une machine Kali Linux. 
