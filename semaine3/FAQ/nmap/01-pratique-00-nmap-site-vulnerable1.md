# Références : 

- https://www.youtube.com/watch?v=c6o73oBIJ3M&ab_channel=r2schools
- https://www.youtube.com/watch?v=900FsYDeluI&ab_channel=Luis
- https://medium.com/@ATTOUCHI.MOHAMED/20-nmap-commands-explained-nmap-gui-4256e91dc0b5
- https://www.youtube.com/watch?v=4t4kBkMsDbQ&t=73s&ab_channel=NetworkChuck



# NMAP 
- Explorons chaque aspect de l'installation, de l'utilisation, et des paramètres de `Nmap`, ainsi que leur signification précise. 
- Nous utiliserons également l'interface graphique **Zenmap** pour compléter cet exercice, avec des explications ligne par ligne sur ce que chaque commande fait. 
- Cet exercice est conçu pour offrir une immersion totale dans l'utilisation de `Nmap` pour scanner le site `testphp.vulnweb.com`.

# Partie 1 : Installation de Nmap et Zenmap

1. **Téléchargement de Nmap pour Windows** :
   - Allez sur le site officiel de Nmap : [https://nmap.org/download.html](https://nmap.org/download.html).
   - Téléchargez le **Windows Installer**.

2. **Installation** :
   - Exécutez le fichier d’installation téléchargé (par exemple, `nmap-setup.exe`).
   - Suivez l’assistant pour installer Nmap, en choisissant d'inclure **Zenmap** dans l'installation. Zenmap est une interface graphique qui facilite l'utilisation de Nmap.

3. **Vérification de l’installation** :
   - Ouvrez **PowerShell** ou l’**Invite de commandes** (touche Windows + `R`, puis tapez `cmd` et appuyez sur Entrée).
   - Tapez la commande suivante pour vérifier que Nmap est bien installé :
     ```bash
     nmap --version
     ```
   - Si Nmap est correctement installé, vous verrez des informations sur la version installée.

---

# Partie 2 : Explication détaillée des commandes et des paramètres de Nmap

Voici les principales commandes et paramètres que vous pouvez utiliser avec `Nmap` pour effectuer des analyses très détaillées. Je vais expliquer chaque option **de façon exhaustive**, ligne par ligne.

#### 1. **Commande de base :**
   ```bash
   nmap testphp.vulnweb.com
   ```
   - Cette commande exécute un **scan TCP SYN basique** sur le site. Elle envoie des paquets SYN aux ports de la cible pour voir lesquels répondent.
   - Par défaut, Nmap scanne **1000 ports les plus communément utilisés** et montre quels ports sont ouverts.
   
#### 2. **Scan de ports spécifique (option `-p`)** :
   ```bash
   nmap -p 80,443 testphp.vulnweb.com
   ```
   - `-p 80,443` : Cette option spécifie les **ports spécifiques** à scanner. Ici, nous scannons uniquement les ports **80 (HTTP)** et **443 (HTTPS)**.

#### 3. **Scan de tous les ports (option `-p-`)** :
   ```bash
   nmap -p- testphp.vulnweb.com
   ```
   - `-p-` : Scanne **tous les 65535 ports** disponibles au lieu des 1000 ports par défaut. Cela prend plus de temps mais est utile pour une analyse exhaustive.

#### 4. **Détection des versions des services (option `-sV`)** :
   ```bash
   nmap -sV testphp.vulnweb.com
   ```
   - `-sV` : Permet de faire une **détection de version des services** en cours d'exécution sur les ports ouverts. Par exemple, si un port est ouvert pour un serveur HTTP, Nmap tentera de déterminer si c’est Apache, Nginx, ou un autre serveur web, ainsi que sa version.
   
#### 5. **Scan furtif (option `-sS`)** :
   ```bash
   nmap -sS testphp.vulnweb.com
   ```
   - `-sS` : Réalise un **scan furtif TCP SYN**, aussi appelé **demi-scan SYN**. Au lieu de compléter la poignée de main TCP complète (SYN-SYN/ACK-ACK), Nmap envoie juste un paquet SYN puis abandonne la connexion, ce qui peut rendre le scan moins détectable par les pare-feux.

#### 6. **Scan de port UDP (option `-sU`)** :
   ```bash
   nmap -sU testphp.vulnweb.com
   ```
   - `-sU` : Exécute un scan UDP, ce qui est utile pour découvrir les services utilisant le protocole **UDP** comme DNS (port 53) ou DHCP.

#### 7. **Détection d’OS (option `-O`)** :
   ```bash
   nmap -O testphp.vulnweb.com
   ```
   - `-O` : Tente de déterminer le **système d'exploitation** de la machine cible (Linux, Windows, etc.) en analysant les réponses TCP/IP.

#### 8. **Scan complet avec détection de vulnérabilités (option `--script vuln`)** :
   ```bash
   nmap --script vuln testphp.vulnweb.com
   ```
   - `--script vuln` : Utilise des scripts NSE (Nmap Scripting Engine) pour rechercher des **vulnérabilités connues** sur les services détectés. C'est extrêmement puissant, car Nmap peut identifier des failles spécifiques telles que **Heartbleed** ou **Shellshock**.

#### 9. **Scan intensif avec plusieurs options activées** :
   ```bash
   nmap -A -T4 testphp.vulnweb.com
   ```
   - `-A` : Active plusieurs fonctionnalités avancées comme la détection d’OS (`-O`), la détection de version (`-sV`), et la cartographie réseau.
   - `-T4` : Utilise un timing plus agressif pour accélérer le scan. Les valeurs vont de **T0** (scan très lent et furtif) à **T5** (scan extrêmement rapide mais plus détectable).

#### 10. **Traceroute (option `--traceroute`)** :
   ```bash
   nmap --traceroute testphp.vulnweb.com
   ```
   - `--traceroute` : Affiche le chemin que prend le paquet pour atteindre la cible. Cela permet d'identifier les routes réseau et les éventuels nœuds intermédiaires.

#### 11. **Sortie détaillée et enregistrement (option `-oN`, `-oX`)** :
   - Pour sauvegarder les résultats dans un fichier texte, utilisez :
     ```bash
     nmap -oN scan_results.txt testphp.vulnweb.com
     ```
     `-oN` : Sauvegarde le résultat en format texte brut.
     
   - Pour générer un fichier XML lisible par machine (utile pour des scripts d’analyse), utilisez :
     ```bash
     nmap -oX scan_results.xml testphp.vulnweb.com
     ```

---

# Partie 3 : Utilisation exhaustive de Zenmap

**Zenmap** est l'interface graphique de Nmap qui permet de visualiser les scans de manière plus accessible.

1. **Lancer Zenmap** :
   - Après installation, ouvrez Zenmap via le menu Démarrer.

2. **Scanner `testphp.vulnweb.com` avec un profil prédéfini** :
   - Dans le champ **Target**, entrez `testphp.vulnweb.com`.
   - Dans le champ **Profile**, sélectionnez **Intense scan** pour un scan complet.

3. **Lancer l'analyse** :
   - Cliquez sur **Scan** pour démarrer l'analyse. Zenmap exécute les commandes Nmap correspondantes et affiche les résultats sous forme d'onglets.

4. **Interpréter les résultats dans Zenmap** :
   - **Nmap Output** : Cet onglet affiche les résultats bruts de Nmap, comme ceux que vous verriez dans la ligne de commande.
   - **Ports/Hosts** : Cette vue liste les **ports ouverts** et les services associés.
   - **Topology** : Cet onglet présente une **carte réseau** montrant les relations entre les hôtes et les services.
   - **Host Details** : Fournit des informations détaillées sur chaque hôte, y compris le système d'exploitation et les versions des services.

---

# Partie 4 : Exemples complets d’utilisation

#### Exemple 1 : Scan d’un large éventail de ports avec détection de version
```bash
nmap -p 1-65535 -sV testphp.vulnweb.com
```
- `-p 1-65535` : Scanne tous les ports possibles (de 1 à 65535).
- `-sV` : Tente de détecter la version des services actifs sur chaque port.

#### Exemple 2 : Scan furtif avec détection d'OS et des scripts NSE
```bash
nmap -sS -O --script vuln testphp.vulnweb.com
```
- `-sS` : Scan furtif TCP SYN.
- `-O` : Détection du système d'exploitation.
- `--script vuln` : Exécute des scripts pour détecter des vulnérabilités connues.

#### Exemple 3 : Scan rapide avec un timing agressif
```bash
nmap -T5 -F testphp.vulnweb.com
```
- `-T5` : Timing très agressif pour un scan rapide.
- `-F` : Limite le scan aux **ports les plus fréquemment utilisés** (un scan rapide des 100 ports les plus courants).

---



# Partie 5 : Questions 

1. Quels sont les avantages d'un scan furtif avec l’option `-sS` par rapport à un scan normal ?
2. Expliquez ce que fait l'option `-sV` dans un scan et pourquoi elle est importante.
3. Pourquoi est-il important de savoir détecter le système d'exploitation d'une machine cible avec `-O` ?
4. Quelles vulnérabilités avez-vous pu découvrir sur `testphp.vulnweb.com` avec `--script vuln` ?
5. En quoi le scan avec `nmap -A` est-il plus complet que d'autres scans ? Quels sont les inconvénients possibles ?
  
### Notes  :

- **Éthique du scan** : Les scans Nmap doivent toujours être faits avec autorisation.
- **Performance** : Discutez de l'impact des options `-T` (timing) sur la vitesse du scan et sur sa détectabilité.
- **Analyse** : Il faut toujours interpréter et documenter les résultats pour développer une méthode rigoureuse d'analyse des réseaux et services.
