# Scanning a Local Vulnerable Website with Nmap

#### Étape 1 : Scanner la Cible
Commencez par exécuter un scan de base pour identifier les ports ouverts sur votre site web vulnérable.

```bash
nmap localhost
```

#### Étape 2 : Scan SYN pour les Ports Ouverts
Un scan SYN est plus furtif et est souvent utilisé pour l'analyse initiale des systèmes cibles.

```bash
nmap -sS localhost
```

#### Étape 3 : Détection des Services et Versions
Pour découvrir les services et leurs versions qui tournent sur les ports ouverts, utilisez :

```bash
nmap -sV localhost
```

#### Étape 4 : Détection du Système d'Exploitation
Pour tenter de déterminer le système d'exploitation de votre serveur local :

```bash
nmap -O localhost
```

#### Étape 5 : Scan Complet avec Détection d'OS et Services
Pour une analyse complète, combinez la détection d'OS et de services avec une détection de script pour les vulnérabilités courantes :

```bash
nmap -A localhost
```

#### Étape 6 : Utilisation des Scripts de Vulnérabilité Nmap
Nmap inclut des scripts pour détecter des vulnérabilités spécifiques. Par exemple, pour exécuter tous les scripts de vulnérabilité :

```bash
nmap --script vuln localhost
```

#### Étape 7 : Enregistrement des Résultats
Pour conserver une trace des résultats du scan, enregistrez-les dans un fichier texte ou XML :

- Format texte :
  ```bash
  nmap -oN resultat.txt localhost
  ```

- Format XML :
  ```bash
  nmap -oX resultat.xml localhost
  ```

### Exercice Pratique : Scan d'un Site Web Local Vulnérable

1. **Scan de Base :**
   ```bash
   nmap localhost
   ```

2. **Scan SYN :**
   ```bash
   nmap -sS localhost
   ```

3. **Détection des Services et Versions :**
   ```bash
   nmap -sV localhost
   ```

4. **Détection du Système d'Exploitation :**
   ```bash
   nmap -O localhost
   ```

5. **Scan Complet :**
   ```bash
   nmap -A localhost
   ```

6. **Détection des Vulnérabilités :**
   ```bash
   nmap --script vuln localhost
   ```

7. **Enregistrement des Résultats :**
   - Format texte :
     ```bash
     nmap -oN resultat.txt localhost
     ```
   - Format XML :
     ```bash
     nmap -oX resultat.xml localhost
     ```

Ces étapes vous permettront d'analyser en profondeur votre site web local vulnérable en utilisant Nmap sur Kali Linux. Les étudiants pourront copier et coller ces commandes pour effectuer les scans nécessaires.
