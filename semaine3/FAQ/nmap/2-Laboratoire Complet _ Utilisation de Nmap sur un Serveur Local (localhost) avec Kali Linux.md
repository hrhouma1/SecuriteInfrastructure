### Laboratoire Complet : Utilisation de Nmap sur un Serveur Local (localhost) avec Kali Linux

#### Objectif
Fournir une série d'exercices pratiques pour apprendre à utiliser Nmap sur un serveur local hébergé sur `localhost/demo`. Ce laboratoire couvre des scans de base, avancés, la détection de services et de systèmes d'exploitation, l'utilisation de scripts Nmap, et plus encore.

### Préparation

1. **Vérifiez que Nmap est installé sur votre machine Kali Linux :**
   ```bash
   sudo apt update
   sudo apt install nmap
   ```

2. **Assurez-vous que votre serveur local est en cours d'exécution sur `localhost/demo`.**

### Étape 1 : Scan de Base

1. **Scan de base de localhost :**
   ```bash
   nmap localhost
   ```

### Étape 2 : Scans SYN

1. **Scan SYN pour détecter les ports ouverts :**
   ```bash
   nmap -sS localhost
   ```

### Étape 3 : Détection des Services et Versions

1. **Détection des services et versions sur les ports ouverts :**
   ```bash
   nmap -sV localhost
   ```

### Étape 4 : Détection du Système d'Exploitation

1. **Détection du système d'exploitation de la machine locale :**
   ```bash
   nmap -O localhost
   ```

### Étape 5 : Scan Complet avec Détection d'OS et de Services

1. **Scan complet incluant la détection d'OS et des services :**
   ```bash
   nmap -A localhost
   ```

### Étape 6 : Utilisation des Scripts Nmap pour la Détection des Vulnérabilités

1. **Utiliser les scripts Nmap pour vérifier les vulnérabilités courantes :**
   ```bash
   nmap --script vuln localhost
   ```

### Étape 7 : Scans Avancés

1. **Scan UDP pour détecter les services UDP :**
   ```bash
   nmap -sU localhost
   ```

2. **Scan de connexion TCP (plus lent mais plus complet) :**
   ```bash
   nmap -sT localhost
   ```

3. **Scan de fenêtre TCP :**
   ```bash
   nmap -sW localhost
   ```

4. **Scan de ACK TCP :**
   ```bash
   nmap -sA localhost
   ```

### Étape 8 : Évasion de Pare-feu et Usurpation

1. **Utiliser des leurres pour masquer l'origine du scan :**
   ```bash
   nmap -D RND:10 localhost
   ```

2. **Fragmenter les paquets pour contourner les pare-feu :**
   ```bash
   nmap -f localhost
   ```

3. **Changer les options de timing pour éviter la détection :**
   ```bash
   nmap -T4 localhost
   ```

### Étape 9 : Enregistrement des Résultats

1. **Enregistrer les résultats en format texte normal :**
   ```bash
   nmap -oN resultat.txt localhost
   ```

2. **Enregistrer les résultats en format XML :**
   ```bash
   nmap -oX resultat.xml localhost
   ```

3. **Enregistrer les résultats en format Grepable :**
   ```bash
   nmap -oG resultat.gnmap localhost
   ```

### Résumé des Commandes

- **Scan de base :** `nmap localhost`
- **Scan SYN :** `nmap -sS localhost`
- **Détection des services et versions :** `nmap -sV localhost`
- **Détection du système d'exploitation :** `nmap -O localhost`
- **Scan complet :** `nmap -A localhost`
- **Détection des vulnérabilités :** `nmap --script vuln localhost`
- **Scan UDP :** `nmap -sU localhost`
- **Scan TCP :** `nmap -sT localhost`
- **Scan fenêtre TCP :** `nmap -sW localhost`
- **Scan ACK TCP :** `nmap -sA localhost`
- **Leurres :** `nmap -D RND:10 localhost`
- **Fragmentation :** `nmap -f localhost`
- **Options de timing :** `nmap -T4 localhost`
- **Enregistrer en texte normal :** `nmap -oN resultat.txt localhost`
- **Enregistrer en XML :** `nmap -oX resultat.xml localhost`
- **Enregistrer en Grepable :** `nmap -oG resultat.gnmap localhost`

Ces exercices permettront aux étudiants de se familiariser avec les différentes fonctionnalités de Nmap et de comprendre comment elles peuvent être utilisées pour analyser et sécuriser des systèmes. Ils n'auront qu'à copier-coller les commandes pour les exécuter sur leur machine Kali Linux.
