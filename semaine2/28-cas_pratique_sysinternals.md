
## 4.3 Cas Pratique : Analyse d'une Activité Suspecte avec Sysinternals

### Introduction :
Dans cet exercice pratique, vous allez utiliser les outils Sysinternals, en particulier Process Explorer et Autoruns, pour analyser une activité suspecte sur un système Windows. L'objectif est d'identifier et de remédier à une menace potentielle, tout en documentant le processus pour un rapport d'incident.

### Étapes de l'Exercice :

1. **Préparation de l'Environnement :**
   - Créez une machine virtuelle ou utilisez une machine de test pour simuler un environnement où une activité suspecte est détectée. Assurez-vous que les outils Sysinternals sont installés et prêts à l'emploi.

2. **Identification de l'Activité Suspecte :**
   - Lancez **Process Explorer** et parcourez les processus actifs. Recherchez des processus avec des noms inhabituels, des signatures numériques manquantes ou incorrectes, ou des utilisations anormales de ressources.
   - Ouvrez **Autoruns** et examinez les programmes configurés pour démarrer automatiquement. Identifiez les entrées qui semblent suspectes ou inconnues.

3. **Analyse Détaillée :**
   - Utilisez Process Explorer pour analyser les propriétés des processus suspects, y compris les handles et les DLLs associés. Examinez les connexions réseau et les fichiers ouverts par ces processus.
   - Dans Autoruns, vérifiez les signatures des programmes de démarrage et recherchez en ligne des informations supplémentaires sur les entrées suspectes.

4. **Remédiation :**
   - Si un processus ou un programme de démarrage est confirmé comme étant malveillant, utilisez Process Explorer pour suspendre ou terminer le processus, et Autoruns pour désactiver ou supprimer l'entrée de démarrage.
   - Déconnectez la machine du réseau pour éviter toute propagation si vous avez identifié une menace sérieuse.

5. **Documentation et Rapport d'Incident :**
   - Documentez chaque étape de votre investigation, y compris les captures d'écran des processus et des entrées analysées. Incluez les actions de remédiation prises et les résultats observés.
   - Rédigez un rapport d'incident complet qui pourra être utilisé pour l'analyse post-mortem et pour améliorer les stratégies de réponse future.

### Conclusion :
Cet exercice pratique vous permet d'appliquer vos connaissances des outils Sysinternals dans un scénario réaliste d'analyse et de réponse à une menace. La documentation de vos actions et la rédaction d'un rapport d'incident sont essentielles pour renforcer vos compétences en sécurité et pour préparer des réponses efficaces aux incidents futurs.


# ANNEXE 4.3.1 - pratique 01

Pour rendre cet exercice encore plus pratique et facile à comprendre, voici une version simplifiée avec des détails supplémentaires :

### 1. **Préparation de l'Environnement :**

- **Créer une machine virtuelle (VM)** : 
  - Si vous n'avez pas encore de machine virtuelle, téléchargez un logiciel comme **VirtualBox** (gratuit) ou **VMware Player**.
  - Installez Windows sur cette VM, comme si vous installiez Windows sur un ordinateur normal. Pensez à garder cette VM isolée (sans connexion internet au début) pour éviter de propager une potentielle menace si vous faites des tests réels.
  
- **Installer Sysinternals** :
  - Rendez-vous sur le site [Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) pour télécharger la suite d'outils. Téléchargez le fichier zip, puis extrayez-le dans un dossier de votre VM.
  - Pour un accès rapide, placez ces outils directement sur le bureau de votre VM.

### 2. **Identification de l'Activité Suspecte :**

- **Ouvrir Process Explorer** :
  - Double-cliquez sur `procexp.exe` pour lancer Process Explorer.
  - **Ce que vous devez faire** : Imaginez que votre ordinateur est un tableau de bord et que Process Explorer est l'écran qui vous montre tout ce qui tourne sous le capot (les programmes, les applications, etc.).
  
  - **Recherche de Processus Suspects** : Cherchez des programmes avec des noms étranges ou des chiffres dans leurs noms, comme `expl0rer.exe` au lieu de `explorer.exe`.
  - **Exemple concret** : Si vous voyez un programme appelé `abc123.exe` qui tourne, alors que vous ne l'avez jamais vu avant, c'est peut-être un signe de problème.

- **Ouvrir Autoruns** :
  - Double-cliquez sur `autoruns.exe`.
  - **Ce que vous devez faire** : Autoruns vous montre tout ce qui se lance automatiquement au démarrage de l'ordinateur. 
  - **Cherchez des éléments bizarres** : Par exemple, si vous voyez un programme qui se lance depuis un dossier temporaire (`C:\Temp`) ou depuis un endroit inhabituel, cela peut être suspect.

### 3. **Analyse Détaillée :**

- **Utiliser Process Explorer** :
  - **Cliquez-droit sur un processus suspect** : Allez dans `Properties` puis dans l'onglet `Image`. Si la ligne "Verified Signer" indique "Unable to Verify", cela peut être un indice que le programme est malveillant.
  - **Examinez les "Handles"** : Les handles sont comme les tentacules d’un programme – ils montrent à quoi il est connecté ou ce qu’il utilise (fichiers, clés de registre, etc.). Vous pouvez les voir sous `Properties > Handles`.
  - **Regardez les connexions réseau** : Si un programme suspect se connecte à une adresse IP bizarre ou inconnue, cela peut aussi être un indice d’activité malveillante.

- **Utiliser Autoruns** :
  - **Recherchez des informations sur les programmes** : Si vous trouvez une entrée suspecte, vous pouvez faire une recherche en ligne avec son nom pour voir si d'autres personnes ont déjà signalé ce programme comme malveillant. 

### 4. **Remédiation (Nettoyage)** :

- **Process Explorer** :
  - **Suspendre un processus** : Cliquez droit sur le processus suspect, puis choisissez `Suspend`. Cela arrête temporairement le processus pour éviter qu'il ne fasse d'autres dégâts pendant que vous investiguez.
  - **Tuer un processus** : Si vous êtes certain qu'un processus est malveillant, vous pouvez le "tuer" en choisissant `Kill Process`.
  
- **Autoruns** :
  - **Désactiver une entrée** : Pour un programme suspect dans Autoruns, vous pouvez simplement décocher la case à côté de son nom. Cela empêchera le programme de se lancer au prochain démarrage.
  - **Supprimer une entrée** : Si vous êtes sûr que c'est malveillant, faites un clic droit sur l'entrée et sélectionnez `Delete`.

- **Déconnecter la machine** : Si vous pensez qu'il y a une menace sérieuse, déconnectez la machine du réseau en cliquant sur l'icône réseau en bas à droite et en choisissant "Désactiver". Cela empêchera le programme de communiquer avec l'extérieur.

### 5. **Documentation et Rapport d'Incident** :

- **Documenter chaque étape** : Prenez des captures d'écran à chaque étape. Par exemple, faites une capture du processus suspect avant et après l'avoir tué.
- **Rédiger un rapport** : Imaginez que vous racontez à quelqu'un ce que vous avez fait, étape par étape. Indiquez quels processus vous avez trouvés, ce que vous avez fait pour les arrêter, et ce que vous avez appris.

### Conclusion :
En suivant ces étapes, vous avez appris à utiliser des outils puissants pour analyser et nettoyer un système Windows en cas de suspicion d'activité malveillante. Cet exercice vous permet non seulement de comprendre comment ces outils fonctionnent, mais aussi de savoir comment réagir face à une menace réelle.

# ANNEXE 4.3.2 - pratique 02 - tuer un processus


Je vous propose comment vous pouvez utiliser des commandes Windows pour identifier et tuer un processus en utilisant le port 8080 comme exemple :

### 1. **Identifier le processus utilisant le port 8080 :**

- **Commande :**
  ```bash
  netstat -ano | findstr :8080
  ```
  - **Explication :**
    - `netstat -ano` affiche toutes les connexions et ports en écoute avec le PID (Process ID) du processus propriétaire.
    - `findstr :8080` filtre les résultats pour n'afficher que les lignes contenant `:8080`, ce qui correspond au port que vous surveillez.

- **Résultat attendu :**
  - La commande retournera une ou plusieurs lignes similaires à celle-ci :
    ```
    TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       1234
    ```
  - Ici, `1234` est le PID du processus qui utilise le port 8080.

### 2. **Tuer le processus utilisant le PID :**

- **Commande :**
  ```bash
  taskkill /F /PID 1234
  ```
  - **Explication :**
    - `taskkill` est la commande utilisée pour terminer un processus.
    - `/F` force l'arrêt du processus.
    - `/PID 1234` spécifie le numéro du PID que vous avez identifié avec `netstat`.

- **Exemple concret :**
  - Si le PID du processus utilisant le port 8080 est `1234`, alors la commande `taskkill /F /PID 1234` mettra fin à ce processus.

### Conclusion :

Ces commandes vous permettent de rapidement identifier et arrêter un processus spécifique qui utilise un port réseau particulier, comme le port 8080. Cette technique est particulièrement utile lorsque vous devez libérer un port ou arrêter un processus potentiellement malveillant sans devoir redémarrer la machine.



## 4.3 Cas Pratique 03: Analyse d'une Activité Suspecte avec Sysinternals

### Introduction :
Dans cet exercice pratique, vous allez utiliser les outils Sysinternals, en particulier **Process Explorer** et **Autoruns**, pour analyser une activité suspecte sur un système Windows. Vous apprendrez également à identifier et à tuer des processus utilisant des commandes Windows. L'objectif est d'identifier et de remédier à une menace potentielle, tout en documentant le processus pour un rapport d'incident.

### Étapes de l'Exercice :

1. **Préparation de l'Environnement :**
   - Créez une machine virtuelle ou utilisez une machine de test pour simuler un environnement où une activité suspecte est détectée. Assurez-vous que les outils Sysinternals sont installés et prêts à l'emploi.

2. **Identification de l'Activité Suspecte :**
   - **Lancez Process Explorer** et parcourez les processus actifs. Recherchez des processus avec des noms inhabituels, des signatures numériques manquantes ou incorrectes, ou des utilisations anormales de ressources.
   - **Ouvrez Autoruns** et examinez les programmes configurés pour démarrer automatiquement. Identifiez les entrées qui semblent suspectes ou inconnues.
   - **Utilisez la commande `netstat`** pour identifier les processus qui utilisent des ports réseau spécifiques (par exemple, le port 8080). Tapez la commande suivante dans l'invite de commande :
     ```bash
     netstat -ano | findstr :8080
     ```
     Cela vous donnera le PID du processus utilisant le port 8080.

3. **Analyse Détaillée :**
   - Utilisez **Process Explorer** pour analyser les propriétés des processus suspects, y compris les handles et les DLLs associés. Examinez les connexions réseau et les fichiers ouverts par ces processus.
   - Dans **Autoruns**, vérifiez les signatures des programmes de démarrage et recherchez en ligne des informations supplémentaires sur les entrées suspectes.
   - **Tuer le processus suspect** en utilisant le PID obtenu avec `netstat`. Utilisez la commande suivante pour arrêter le processus :
     ```bash
     taskkill /F /PID 1234
     ```
     Remplacez `1234` par le PID que vous avez identifié.

4. **Remédiation :**
   - Si un processus ou un programme de démarrage est confirmé comme étant malveillant, utilisez **Process Explorer** pour suspendre ou terminer le processus, et **Autoruns** pour désactiver ou supprimer l'entrée de démarrage.
   - **Déconnectez la machine du réseau** pour éviter toute propagation si vous avez identifié une menace sérieuse.

5. **Documentation et Rapport d'Incident :**
   - Documentez chaque étape de votre investigation, y compris les captures d'écran des processus et des entrées analysées. Incluez les actions de remédiation prises et les résultats observés.
   - Rédigez un rapport d'incident complet qui pourra être utilisé pour l'analyse post-mortem et pour améliorer les stratégies de réponse future.

### Conclusion :
Cet exercice pratique vous permet d'appliquer vos connaissances des outils Sysinternals et des commandes Windows dans un scénario réaliste d'analyse et de réponse à une menace. La documentation de vos actions et la rédaction d'un rapport d'incident sont essentielles pour renforcer vos compétences en sécurité et pour préparer des réponses efficaces aux incidents futurs.

En intégrant les commandes `netstat` et `taskkill` dans cet exercice, vous apprenez non seulement à identifier des processus suspects, mais aussi à les neutraliser directement via l'invite de commande, ce qui renforce votre capacité à réagir rapidement en cas de menace.
