### Tutoriel Nmap sur Kali Linux

#### Introduction
Nmap (Network Mapper) est un outil open-source utilisé pour la découverte de réseaux et l'audit de sécurité. Il est très puissant mais peut être utilisé de manière simple pour commencer. Ce tutoriel est conçu pour les débutants, avec des explications et des exercices simples.

#### Objectifs
- Comprendre les bases de Nmap
- Apprendre à effectuer des scans simples avec Nmap
- Pratiquer avec des exercices sur Kali Linux

#### Pré-requis
- Une installation de Kali Linux
- Accès à un réseau local ou à des machines virtuelles pour pratiquer

#### 1. Installation de Nmap
Nmap est pré-installé sur Kali Linux. Pour vérifier si Nmap est installé, ouvrez un terminal et tapez :
```sh
nmap --version
```
Si Nmap n'est pas installé, vous pouvez l'installer avec la commande suivante :
```sh
sudo apt-get install nmap
```

#### 2. Commandes de Base de Nmap

##### 2.1. Scan d'un Hôte Unique
Pour scanner un seul hôte, utilisez la commande suivante :
```sh
nmap <adresse_IP>
```
**Exemple :**
```sh
nmap 192.168.1.1
```
Cela effectuera un scan de base sur l'hôte avec l'adresse IP 192.168.1.1.

##### 2.2. Scan d'une Plage d'Adresses IP
Pour scanner une plage d'adresses IP :
```sh
nmap 192.168.1.1-254
```
Cela scannerait toutes les adresses IP de 192.168.1.1 à 192.168.1.254.

##### 2.3. Scan de Tous les Ports
Par défaut, Nmap ne scanne que les 1000 ports les plus courants. Pour scanner tous les ports (1-65535) :
```sh
nmap -p- <adresse_IP>
```
**Exemple :**
```sh
nmap -p- 192.168.1.1
```

##### 2.4. Scan en Mode Verbeux
Pour obtenir plus de détails pendant le scan :
```sh
nmap -v <adresse_IP>
```
**Exemple :**
```sh
nmap -v 192.168.1.1
```

##### 2.5. Scan SYN
Le scan SYN est rapide et discret car il n'établit pas de connexion complète :
```sh
sudo nmap -sS <adresse_IP>
```
**Exemple :**
```sh
sudo nmap -sS 192.168.1.1
```

##### 2.6. Détection de Version
Pour détecter les versions des services en cours d'exécution sur les ports ouverts :
```sh
nmap -sV <adresse_IP>
```
**Exemple :**
```sh
nmap -sV 192.168.1.1
```

##### 2.7. Détection du Système d'Exploitation
Pour tenter de détecter le système d'exploitation d'une machine :
```sh
sudo nmap -O <adresse_IP>
```
**Exemple :**
```sh
sudo nmap -O 192.168.1.1
```

#### 3. Exercices Pratiques

##### Exercice 1 : Scan Basique d'un Hôte
1. Choisissez une machine sur votre réseau (ou utilisez une machine virtuelle).
2. Ouvrez un terminal sur Kali Linux.
3. Exécutez la commande suivante pour scanner l'hôte choisi :
   ```sh
   nmap <adresse_IP>
   ```
4. Notez les résultats et identifiez les ports ouverts.

##### Exercice 2 : Scan de Tous les Ports d'une Plage d'Adresses
1. Choisissez une plage d'adresses IP sur votre réseau.
2. Exécutez la commande suivante pour scanner cette plage :
   ```sh
   nmap -p- <plage_adresses_IP>
   ```
3. Analysez les résultats et identifiez les hôtes et ports ouverts.

##### Exercice 3 : Scan SYN
1. Choisissez une machine sur votre réseau.
2. Exécutez la commande suivante pour effectuer un scan SYN :
   ```sh
   sudo nmap -sS <adresse_IP>
   ```
3. Notez les différences avec le scan de base.

##### Exercice 4 : Détection de Version
1. Choisissez une machine sur votre réseau.
2. Exécutez la commande suivante pour détecter les versions des services :
   ```sh
   nmap -sV <adresse_IP>
   ```
3. Notez les versions des services détectés.

##### Exercice 5 : Détection du Système d'Exploitation
1. Choisissez une machine sur votre réseau.
2. Exécutez la commande suivante pour tenter de détecter le système d'exploitation :
   ```sh
   sudo nmap -O <adresse_IP>
   ```
3. Notez le système d'exploitation détecté et comparez avec la réalité.

#### Conclusion
Ce tutoriel vous a fourni les bases pour commencer à utiliser Nmap. Pratiquez ces commandes et explorez davantage les fonctionnalités de Nmap pour devenir plus à l'aise avec cet outil puissant. N'hésitez pas à poser des questions ou à demander de l'aide si vous en avez besoin.

### Liens Utiles
- [Documentation Officielle de Nmap](https://nmap.org/book/man.html)
- [Tutoriels et Guides Nmap](https://nmap.org/docs.html)

Bon scan !
