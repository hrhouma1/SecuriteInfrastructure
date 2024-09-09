### Laboratoire Complet : Utilisation de Nmap sur un Réseau Local avec Kali Linux

#### Objectif
Fournir une série d'exercices pratiques pour apprendre à utiliser Nmap afin de scanner non seulement `localhost`, mais aussi d'autres machines connectées au même réseau NAT. Ce laboratoire couvre des scans de base, avancés, la détection de services et de systèmes d'exploitation, l'utilisation de scripts Nmap, et plus encore.

### Préparation

1. **Vérifiez que Nmap est installé sur votre machine Kali Linux :**
   ```bash
   sudo apt update
   sudo apt install nmap
   ```

2. **Assurez-vous que votre serveur local et les autres machines sont connectés au même réseau NAT.**

### Étape 1 : Scanner les Machines sur le Réseau Local

1. **Scanner tous les hôtes connectés au même réseau :**
   ```bash
   nmap -sn 192.168.1.0/24
   ```

### Étape 2 : Scans SYN sur le Réseau Local

1. **Scan SYN pour détecter les ports ouverts sur tous les hôtes :**
   ```bash
   nmap -sS 192.168.1.0/24
   ```

### Étape 3 : Détection des Services et Versions sur le Réseau Local

1. **Détection des services et versions sur les ports ouverts :**
   ```bash
   nmap -sV 192.168.1.0/24
   ```

### Étape 4 : Détection du Système d'Exploitation sur le Réseau Local

1. **Détection du système d'exploitation des machines sur le réseau :**
   ```bash
   nmap -O 192.168.1.0/24
   ```

### Étape 5 : Scan Complet avec Détection d'OS et de Services

1. **Scan complet incluant la détection d'OS et des services sur tout le réseau :**
   ```bash
   nmap -A 192.168.1.0/24
   ```

### Étape 6 : Utilisation des Scripts Nmap pour la Détection des Vulnérabilités

1. **Utiliser les scripts Nmap pour vérifier les vulnérabilités courantes :**
   ```bash
   nmap --script vuln 192.168.1.0/24
   ```

### Étape 7 : Scans Avancés sur le Réseau Local

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

### Étape 8 : Évasion de Pare-feu et Usurpation

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

### Étape 9 : Enregistrement des Résultats

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

### Résumé des Commandes

- **Scan de base sur le réseau :** `nmap -sn 192.168.1.0/24`
- **Scan SYN :** `nmap -sS 192.168.1.0/24`
- **Détection des services et versions :** `nmap -sV 192.168.1.0/24`
- **Détection du système d'exploitation :** `nmap -O 192.168.1.0/24`
- **Scan complet :** `nmap -A 192.168.1.0/24`
- **Détection des vulnérabilités :** `nmap --script vuln 192.168.1.0/24`
- **Scan UDP :** `nmap -sU 192.168.1.0/24`
- **Scan TCP :** `nmap -sT 192.168.1.0/24`
- **Scan fenêtre TCP :** `nmap -sW 192.168.1.0/24`
- **Scan ACK TCP :** `nmap -sA 192.168.1.0/24`
- **Leurres :** `nmap -D RND:10 192.168.1.0/24`
- **Fragmentation :** `nmap -f 192.168.1.0/24`
- **Options de timing :** `nmap -T4 192.168.1.0/24`
- **Enregistrer en texte normal :** `nmap -oN resultat.txt 192.168.1.0/24`
- **Enregistrer en XML :** `nmap -oX resultat.xml 192.168.1.0/24`
- **Enregistrer en Grepable :** `nmap -oG resultat.gnmap 192.168.1.0/24`

### Exemple d'Exercice Pratique : Scan du Réseau Local

1. **Scan de base du réseau :**
   ```bash
   nmap -sn 192.168.1.0/24
   ```

2. **Scan SYN du réseau :**
   ```bash
   nmap -sS 192.168.1.0/24
   ```

3. **Détection des services et versions sur le réseau :**
   ```bash
   nmap -sV 192.168.1.0/24
   ```

4. **Détection du système d'exploitation sur le réseau :**
   ```bash
   nmap -O 192.168.1.0/24
   ```

5. **Scan complet du réseau :**
   ```bash
   nmap -A 192.168.1.0/24
   ```

6. **Détection des vulnérabilités sur le réseau :**
   ```bash
   nmap --script vuln 192.168.1.0/24
   ```

7. **Scan de fenêtre TCP du réseau :**
   ```bash
   nmap -sW 192.168.1.0/24
   ```

8. **Scan de ACK TCP du réseau :**
   ```bash
   nmap -sA 192.168.1.0/24
   ```

9. **Enregistrer les résultats en texte normal :**
    ```bash
    nmap -oN resultat.txt 192.168.1.0/24
    ```

10. **Enregistrer les résultats en XML :**
    ```bash
    nmap -oX resultat.xml 192.168.1.0/24
    ```

11. **Enregistrer les résultats en JSON :**
    ```bash
    nmap -oJ resultat.json 192.168.1.0/24
    ```

Ces exercices permettront aux étudiants de se familiariser avec les différentes fonctionnalités de Nmap et de comprendre comment elles peuvent être utilisées pour analyser et sécuriser des systèmes sur un réseau local. Ils n'auront qu'à copier-coller les commandes pour les exécuter sur leur machine Kali Linux.
