### Plan de Contenu : Commandes Nmap sous Kali Linux

#### Module 1 : Introduction à Kali Linux et Nmap
- Vue d'ensemble de Kali Linux
- Introduction à Nmap
  - Qu'est-ce que Nmap ?
  - Importance de Nmap dans la sécurité réseau
- Installation et mise à jour de Nmap sur Kali Linux

```bash
sudo apt update
sudo apt install nmap
```

#### Module 2 : Commandes de Base de Nmap
- Syntaxe des commandes Nmap
- Découverte simple d'hôtes
  ```bash
  nmap <cible>
  ```
- Scanner plusieurs hôtes
  ```bash
  nmap <cible1> <cible2> <cible3>
  ```

#### Module 3 : Techniques de Scan Avancées
- Scan de connexion TCP
  ```bash
  nmap -sT <cible>
  ```
- Scan SYN
  ```bash
  nmap -sS <cible>
  ```
- Scan UDP
  ```bash
  nmap -sU <cible>
  ```
- Scan TCP ACK
  ```bash
  nmap -sA <cible>
  ```
- Scan de fenêtre TCP
  ```bash
  nmap -sW <cible>
  ```

#### Module 4 : Détection d'OS et de Services
- Détecter les systèmes d'exploitation
  ```bash
  nmap -O <cible>
  ```
- Détecter les services et versions
  ```bash
  nmap -sV <cible>
  ```
- Combinaison de la détection d'OS et de services
  ```bash
  nmap -A <cible>
  ```

#### Module 5 : Cartographie Réseau et Inventaire
- Scanner des sous-réseaux
  ```bash
  nmap -sn <sous-réseau>
  ```
- Créer des cartes réseau
  ```bash
  nmap -sn -oX carte_reseau.xml <sous-réseau>
  ```
- Utilisation des scripts Nmap
  ```bash
  nmap --script <nom_script> <cible>
  ```
  Exemple :
  ```bash
  nmap --script vuln <cible>
  ```

#### Module 6 : Évasion de Pare-feu et Usurpation
- Utilisation de leurres
  ```bash
  nmap -D RND:10 <cible>
  ```
- Fragmentation
  ```bash
  nmap -f <cible>
  ```
- Options de timing
  ```bash
  nmap -T<0-5> <cible>
  ```

#### Module 7 : Rapport et Sortie
- Enregistrer les résultats des scans
  ```bash
  nmap -oN resultat.txt <cible>
  ```
  ```bash
  nmap -oX resultat.xml <cible>
  ```
  ```bash
  nmap -oG resultat.gnmap <cible>
  ```

#### Module 8 : Exercices Pratiques et Études de Cas
- Laboratoire pratique : Scanner un réseau avec Nmap
  ```bash
  nmap -sn 192.168.1.0/24
  nmap -sS -iL hosts_decouverts.txt
  nmap -A -iL hosts_decouverts.txt
  nmap -oX resultats_scan.xml -iL hosts_decouverts.txt
  ```

#### Module 9 : Considérations Éthiques et Bonnes Pratiques
- Aspects légaux du scan réseau
- Utilisation éthique de Nmap
- Bonnes pratiques pour la sécurité réseau

### Commandes Détaillées et Explications

#### Découverte Simple d'Hôtes
```bash
nmap <cible>
```
- Cette commande de base scanne l'adresse IP ou le nom d'hôte spécifié.

#### Scan SYN
```bash
nmap -sS <cible>
```
- Cette commande effectue un scan SYN, qui est moins susceptible d'être enregistré par le système cible.

#### Détection de l'OS
```bash
nmap -O <cible>
```
- Cette commande tente de déterminer le système d'exploitation de la cible.

#### Détection des Versions de Services
```bash
nmap -sV <cible>
```
- Cette commande sonde les ports ouverts pour déterminer le service et la version en cours d'exécution sur la cible.

#### Utilisation des Scripts Nmap
```bash
nmap --script vuln <cible>
```
- Cette commande utilise le script `vuln` pour vérifier les vulnérabilités sur le système cible.

#### Enregistrement des Résultats des Scans
```bash
nmap -oN resultat.txt <cible>
```
- Cette commande enregistre les résultats du scan dans un format texte normal.

### Exemple d'Exercice Pratique

**Exercice : Scanner un Réseau d'Entreprise**
1. Scanner le sous-réseau `192.168.1.0/24` pour découvrir les hôtes actifs.
   ```bash
   nmap -sn 192.168.1.0/24
   ```
2. Effectuer un scan SYN sur les hôtes découverts.
   ```bash
   nmap -sS -iL hosts_decouverts.txt
   ```
3. Détecter les systèmes d'exploitation et les versions des services.
   ```bash
   nmap -A -iL hosts_decouverts.txt
   ```
4. Enregistrer les résultats au format XML.
   ```bash
   nmap -oX resultats_scan.xml -iL hosts_decouverts.txt
   ```
