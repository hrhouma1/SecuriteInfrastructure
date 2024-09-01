# Plan
# *Analyse des activités systèmes et réseaux*
## **Table des matières**

1. [Présentation de l’audit](#presentation-de-laudit)
2. [Audit avancé et PowerShell](#audit-avance-et-powershell)
3. [Analyse des journaux des événements](#analyse-des-journaux-des-evenements)
4. [Examen du trafic réseau avec Microsoft Message Analyzer](#examen-du-trafic-reseau-avec-microsoft-message-analyzer)
5. [Trafic SMB : Sécurisation et analyse](#trafic-smb-securisation-et-analyse)



---

# **1. Présentation de l’audit** <a id="presentation-de-laudit"></a>

## **1.1 Objectifs**

L'objectif de cette section est de vous introduire aux bases de l’audit dans le contexte des systèmes informatiques, en particulier dans une infrastructure Microsoft. Nous allons expliquer ce qu’est un audit, pourquoi il est important, et quels sont les outils de base que vous pouvez utiliser pour effectuer un audit.

**Sous-objectifs :**
- **Comprendre ce qu’est un audit** : Vous saurez ce que signifie "auditer" un système, avec des exemples simples.
- **Comprendre pourquoi l’audit est important** : Vous verrez pourquoi il est essentiel de surveiller et de vérifier ce qui se passe sur vos systèmes informatiques.
- **Connaître les outils d’audit de base dans Windows** : Vous découvrirez quelques outils que Windows met à votre disposition pour réaliser des audits.

## **1.2 Introduction aux concepts de l’audit**

**Qu’est-ce qu’un audit ?**

L’audit informatique est un processus systématique de collecte, d’évaluation et de vérification d'informations provenant d’un système informatique. L'objectif est de déterminer si les contrôles, les politiques et les procédures d'une entreprise sont respectés, efficaces et contribuent à la sécurité de l'environnement informatique.

Imaginez que votre système informatique est une maison. Un audit, c’est un peu comme faire un tour complet de votre maison pour vérifier que toutes les portes et fenêtres sont bien fermées, que tout fonctionne correctement, et qu’il n’y a pas de problèmes cachés.

Dans le monde informatique, auditer un système signifie vérifier et analyser ce qui se passe dans vos ordinateurs et réseaux. Par exemple, vous pouvez auditer pour voir qui s'est connecté, quelles modifications ont été faites, ou si quelqu'un a essayé d'accéder à des informations sensibles sans autorisation.

**Pourquoi est-ce important ?**

Si vous ne vérifiez jamais votre maison, vous ne saurez pas si une porte est restée ouverte ou si quelque chose a été volé. De la même manière, si vous ne faites jamais d’audit de votre système informatique, vous pourriez passer à côté de problèmes sérieux, comme des failles de sécurité ou des erreurs dans la configuration.

Faire un audit vous aide à :
- **Protéger vos données** : En vous assurant que seules les bonnes personnes y ont accès.
- **Respecter la loi** : Certaines lois exigent que vous gardiez une trace de ce qui se passe dans vos systèmes.
- **Améliorer les performances** : En identifiant ce qui ne fonctionne pas bien et en le corrigeant.



## **1.3 Comprendre l’importance de l’audit dans une infrastructure Microsoft**

Microsoft est l’un des fournisseurs les plus courants pour les systèmes informatiques des entreprises. Si vous utilisez des systèmes Windows, il est crucial de savoir comment auditer ces systèmes pour garantir leur sécurité et leur bon fonctionnement.

**Exemple concret :**
- Imaginez que vous travaillez dans une entreprise où les employés doivent se connecter à des ordinateurs pour faire leur travail. Sans audit, vous ne sauriez pas si quelqu'un essaie de se connecter en utilisant les informations d'un autre employé ou si un logiciel malveillant tente de modifier des fichiers importants.
  
En faisant des audits réguliers, vous pouvez :
- **Surveiller qui fait quoi sur le système** : Par exemple, vous saurez si quelqu'un a essayé de se connecter à un fichier confidentiel sans autorisation.
- **Repérer les anomalies** : Comme des tentatives de connexion à des heures inhabituelles, ou des erreurs fréquentes dans certaines applications.
- **Prévenir les incidents** : En détectant les problèmes avant qu’ils ne deviennent graves, vous pouvez éviter des pannes ou des failles de sécurité.

# RÉSUMÉ:

Dans une infrastructure Microsoft, l’audit joue un rôle essentiel pour garantir la sécurité et la conformité. Les systèmes Windows offrent plusieurs fonctionnalités et outils qui facilitent la mise en place d’un processus d’audit rigoureux. 

**Raisons spécifiques de l’audit dans un environnement Microsoft :**
- **Contrôle des accès** : S’assurer que seuls les utilisateurs autorisés accèdent aux ressources critiques.
- **Surveillance des actions** : Suivre les actions des utilisateurs et des administrateurs pour prévenir les abus et identifier les comportements suspects.
- **Gestion des incidents** : Permet de reconstituer les événements en cas de problème pour comprendre la cause et améliorer les défenses.


## **1.4 Contenu**

Dans cette section, nous allons explorer les bases de l’audit et les outils simples que vous pouvez utiliser dans Windows pour effectuer des audits.

## **1.4.1 Les bases de l’audit**



**Les types d’audit :**

- **Audit de Sécurité** : C’est comme vérifier si toutes les portes et fenêtres de votre maison sont bien fermées. Vous regardez si les données de votre système sont bien protégées et si les personnes non autorisées ne peuvent pas y accéder.
- **Audit de Conformité** : Imaginez que vous avez des règles strictes dans votre maison, comme ne jamais laisser de nourriture sur la table. Un audit de conformité vérifierait si ces règles sont bien respectées. Dans un système informatique, cela signifie vérifier que les politiques et les lois sont suivies.
- **Audit Opérationnel** : Ici, on regarde si tout fonctionne correctement. C’est comme vérifier que tous les appareils de votre maison (comme le four ou la télé) fonctionnent bien. Dans un système informatique, cela revient à s'assurer que les processus sont efficaces et que le système fonctionne bien.

**Pour résumé, les concepts clés à retenir pour les types d'audit sont :**
- **Audit de Sécurité** : Processus visant à identifier les vulnérabilités et à évaluer la posture de sécurité d'un système informatique.
- **Audit de Conformité** : Vérification de l'alignement des pratiques avec les lois, réglementations, normes et politiques internes.
- **Audit Opérationnel** : Évaluation de l'efficacité et de l'efficience des processus opérationnels, souvent utilisé pour optimiser les performances du système.



**Le processus d’audit :**

1. **Planification** : Décider quoi vérifier, par exemple, les tentatives de connexion au système.
2. **Collecte des données** : Rassembler les informations sur ce qui se passe dans le système, comme les journaux de connexion.
3. **Analyse** : Regarder ces données pour repérer des anomalies, comme des connexions à des heures bizarres.
4. **Rapport** : Écrire un rapport sur ce que vous avez trouvé, par exemple, que quelqu’un a essayé de se connecter à 3h du matin.
5. **Suivi des actions correctives** : Corriger les problèmes détectés, comme bloquer l’accès à quelqu’un qui a tenté de se connecter sans autorisation.

## **1.4.2 Outils d’audit disponibles dans Windows**

**Quelques outils simples pour commencer :**

- **Event Viewer (Observateur d'événements)** : C’est un peu comme une caméra de surveillance qui enregistre tout ce qui se passe sur votre système. Vous pouvez y voir qui s’est connecté, quelles erreurs sont survenues, etc.
- **Group Policy Audit Settings (Paramètres d’audit des stratégies de groupe)** : Vous pouvez configurer ces paramètres pour dire au système ce que vous voulez surveiller, comme les connexions aux fichiers importants.
- **PowerShell** : C’est un outil puissant que vous pouvez utiliser pour automatiser des tâches, y compris les audits. Par exemple, vous pouvez écrire un petit programme qui vérifie chaque jour les nouvelles connexions au système.
- **Microsoft Advanced Threat Analytics (ATA)** : Cet outil analyse les comportements sur le réseau pour détecter des menaces potentielles, comme si quelqu’un essaie de voler des données.

# RÉSUMÉ:

Windows propose une variété d'outils pour réaliser des audits complets, parmi lesquels :

- **Event Viewer (Observateur d'événements)** : Un outil intégré qui permet de consulter et d’analyser les journaux des événements pour détecter des anomalies ou des tentatives d’intrusion.
- **Group Policy Audit Settings** : Les paramètres de stratégie de groupe peuvent être configurés pour auditer divers types d’événements, tels que les tentatives de connexion, les accès aux fichiers, etc.
- **PowerShell** : PowerShell permet d’automatiser la collecte de données d’audit et d’effectuer des audits avancés grâce à des scripts personnalisés.
- **Microsoft Advanced Threat Analytics (ATA)** : Un outil pour détecter les menaces en analysant les activités réseau et les comportements des utilisateurs.

[Retour en haut](#plan)



---
---
---

# **2. Audit avancé et PowerShell** <a id="audit-avance-et-powershell"></a>


### **Objectifs**

- **Approfondir les techniques d’audit avec PowerShell :**
  - Comprendre l’utilisation de PowerShell pour automatiser les tâches d’audit.
  - Apprendre à configurer et à utiliser des cmdlets PowerShell spécifiques pour l’audit des systèmes et réseaux.
  - Explorer des scénarios avancés où l’audit avec PowerShell peut être appliqué pour renforcer la sécurité et la conformité.

- **Automatisation des processus d’audit :**
  - Développer des scripts PowerShell pour automatiser la collecte et l’analyse des données d’audit.
  - Utiliser PowerShell pour générer des rapports d’audit détaillés et les partager avec les parties prenantes.
  - Intégrer PowerShell dans un flux de travail DevOps pour assurer un audit continu des systèmes.

---

### **Contenu**

#### **1. Introduction à l’audit avec PowerShell**

- **Pourquoi utiliser PowerShell pour l’audit ?**
  - PowerShell offre une puissante interface en ligne de commande et un langage de script qui permet aux administrateurs système de contrôler et d’automatiser presque tous les aspects d’un système Windows.
  - Il est possible de créer des scripts pour surveiller les événements de sécurité, collecter les journaux d’audit et configurer les paramètres de sécurité à travers toute l’infrastructure.
  - PowerShell est nativement intégré à Windows, ce qui facilite son adoption pour l’audit dans les environnements Microsoft.

#### **2. Scripts PowerShell pour l’audit**

- **Cmdlets de base pour l’audit**
  - **Get-EventLog** : Récupère les journaux d’événements à partir de systèmes locaux et distants. 
    - Exemple : `Get-EventLog -LogName Security -Newest 1000` pour obtenir les 1000 derniers événements de sécurité.
  - **Get-WinEvent** : Offre une méthode plus flexible et performante pour récupérer les événements, y compris ceux des journaux personnalisés.
    - Exemple : `Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624}` pour obtenir les événements d’ouverture de session (ID 4624) à partir du journal de sécurité.
  - **Get-AuditPolicy** : Permet de consulter les stratégies d’audit actuelles sur un système.
    - Exemple : `Get-AuditPolicy -Category *` pour lister toutes les catégories de stratégie d’audit et leurs sous-catégories.
  - **Set-AuditPolicy** : Configure les stratégies d’audit pour répondre aux exigences de sécurité.
    - Exemple : `Set-AuditPolicy -Category Logon/Logoff -Success Enable -Failure Enable` pour auditer les succès et les échecs de connexion.

- **Scripts d’audit avancé**
  - **Script d’audit des modifications de groupes privilégiés :**
    - Objectif : Surveiller les modifications dans les groupes comme les Administrateurs, Domain Admins, etc.
    - Exemple de script :
      ```powershell
      $events = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4728,4732,4756} 
      foreach ($event in $events) {
          $message = $event.Message
          $timeCreated = $event.TimeCreated
          Write-Output "$timeCreated : $message"
      }
      ```
    - Ce script récupère les événements où des utilisateurs ont été ajoutés à des groupes privilégiés et les affiche avec un horodatage.
  
  - **Automatisation de la génération de rapports :**
    - Objectif : Automatiser la création de rapports d’audit pour les examiner périodiquement.
    - Exemple de script :
      ```powershell
      $reportPath = "C:\AuditReports\AuditReport_$(Get-Date -Format 'yyyyMMdd').txt"
      $auditData = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624,4634} | Select-Object TimeCreated, Id, Message
      $auditData | Out-File $reportPath
      ```
    - Ce script génère un rapport des événements d’ouverture et de fermeture de session et le sauvegarde dans un fichier texte.

#### **3. Exemples pratiques d’audit avancé**

- **Audit des accès non autorisés :**
  - Détection des tentatives de connexion échouées et des verrouillages de comptes.
  - Utilisation de PowerShell pour identifier les utilisateurs qui tentent d’accéder sans autorisation.
  - Exemple :
    ```powershell
    $failedLogons = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4625} | Select-Object TimeCreated, Message
    $failedLogons | Where-Object {$_.Message -match "Account Name:\s+(\S+)" } | Sort-Object TimeCreated | Format-Table -AutoSize
    ```

- **Surveillance des modifications des fichiers critiques :**
  - Configuration d’un audit pour surveiller l’accès et les modifications des fichiers critiques du système.
  - Exemple de script PowerShell pour surveiller les modifications de fichiers dans un répertoire sensible :
    ```powershell
    $path = "C:\CriticalFiles"
    $filter = "*.*"
    $watcher = New-Object System.IO.FileSystemWatcher
    $watcher.Path = $path
    $watcher.Filter = $filter
    $watcher.IncludeSubdirectories = $true
    $watcher.NotifyFilter = [System.IO.NotifyFilters]'FileName, LastWrite, LastAccess'

    $watcher.Changed | ForEach-Object {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        Write-Output "$timestamp : Le fichier $($_.FullPath) a été modifié."
    }
    $watcher.EnableRaisingEvents = $true
    ```
    - Ce script surveille un répertoire pour toute modification de fichier et enregistre les changements avec un horodatage.



[Retour en haut](#plan)







---
---
---

## **3. Analyse des journaux des événements** <a id="analyse-des-journaux-des-evenements"></a>

### Objectifs
- Comprendre l’analyse des journaux des événements
- Apprendre à identifier les anomalies

### Contenu
- Accès aux journaux des événements
- Analyse et interprétation des données des journaux

[Retour en haut](#plan)


---
---
---





## **4. Examen du trafic réseau avec Microsoft Message Analyzer** <a id="examen-du-trafic-reseau-avec-microsoft-message-analyzer"></a>

### **Objectifs**

- **Analyser le trafic réseau à l’aide de Microsoft Message Analyzer :**
  - Comprendre l’utilisation de Microsoft Message Analyzer pour capturer, analyser et diagnostiquer le trafic réseau au sein d’une infrastructure Microsoft.
  - Apprendre à configurer et à interpréter les différentes vues et filtres dans Microsoft Message Analyzer pour une analyse efficace du trafic.

- **Identifier et résoudre les problèmes de réseau :**
  - Utiliser Microsoft Message Analyzer pour identifier les problèmes courants de réseau tels que les latences, les erreurs de communication, et les problèmes de connectivité.
  - Apprendre à corréler les événements réseau avec les journaux d’événements système pour diagnostiquer les causes profondes des problèmes.

---

### **Contenu**

#### **1. Introduction à Microsoft Message Analyzer**

- **Présentation de l’outil :**
  - **Microsoft Message Analyzer** est un outil de capture et d’analyse de trafic réseau, qui succède à Network Monitor. Il permet non seulement de capturer le trafic, mais aussi d’analyser les journaux d’événements, les traces et les fichiers de log de diverses sources.
  - **Principales fonctionnalités :**
    - Capture du trafic en temps réel ou importation de fichiers de capture existants (tels que .cap, .etl, .log).
    - Analyse des protocoles réseau (TCP/IP, HTTP, SMB, etc.).
    - Filtrage et tri des données capturées pour se concentrer sur des événements spécifiques.
    - Outils de visualisation pour mieux comprendre le flux de trafic et les interactions réseau.

- **Installation et configuration de Microsoft Message Analyzer :**
  - **Installation :**
    - Téléchargez et installez Microsoft Message Analyzer depuis le Centre de téléchargement Microsoft.
    - Suivez les instructions d'installation et configurez les paramètres initiaux.
  - **Configuration :**
    - Configuration des adaptateurs réseau pour la capture.
    - Paramétrage des filtres de capture pour cibler des types de trafic spécifiques.
    - Sauvegarde des paramètres de capture pour des analyses futures.

#### **2. Études de cas d’analyse de trafic réseau**

- **Cas pratique 1 : Dépannage des problèmes de latence réseau**
  - **Objectif :** Identifier les sources de latence dans le réseau.
  - **Étapes :**
    1. **Capture du trafic :** Configurer Microsoft Message Analyzer pour capturer le trafic sur l’interface réseau concernée.
    2. **Filtrage :** Appliquer des filtres pour ne capturer que les paquets TCP ou HTTP liés aux services affectés par la latence.
    3. **Analyse :** Utiliser les outils d’analyse pour mesurer le temps de réponse et identifier les goulots d'étranglement.
    4. **Résolution :** Proposer des solutions basées sur les résultats, comme l'optimisation des routes réseau ou l'ajustement des configurations de serveur.

  - **Exemple de filtre :**
    ```plaintext
    Tcp.DstPort == 80 && Tcp.Flags.Syn == 1
    ```
    - Ce filtre capture les paquets SYN du protocole TCP destinés au port 80 (HTTP), utiles pour analyser les connexions web.

- **Cas pratique 2 : Analyse des communications SMB (Server Message Block)**
  - **Objectif :** Diagnostiquer les problèmes liés aux partages de fichiers sur le réseau via SMB.
  - **Étapes :**
    1. **Capture :** Démarrer la capture en ciblant les paquets SMB.
    2. **Filtrage :** Isoler les paquets liés à SMB pour se concentrer sur ce protocole.
    3. **Analyse :** Identifier les erreurs et les délais dans les transactions SMB.
    4. **Résolution :** Proposer des ajustements dans les configurations SMB pour améliorer les performances et la sécurité.

  - **Exemple de filtre :**
    ```plaintext
    smb2
    ```
    - Ce filtre permet de capturer uniquement les paquets utilisant le protocole SMB2.

- **Cas pratique 3 : Détection des attaques réseau**
  - **Objectif :** Utiliser Microsoft Message Analyzer pour détecter et analyser une attaque réseau, telle qu'une attaque par déni de service (DDoS).
  - **Étapes :**
    1. **Capture :** Configurer une capture en continu pour surveiller les tentatives de connexion suspectes.
    2. **Analyse :** Identifier des modèles de trafic anormaux, tels que des volumes élevés de requêtes provenant d'une seule source.
    3. **Résolution :** Proposer des mesures de sécurité, comme le blocage des adresses IP suspectes ou le durcissement des configurations de pare-feu.

  - **Exemple de filtre :**
    ```plaintext
    ip.src == 192.168.1.100 && Tcp.DstPort == 443
    ```
    - Ce filtre capture tout le trafic provenant de l’adresse IP 192.168.1.100 à destination du port 443 (HTTPS), utile pour détecter une tentative d'attaque spécifique.

---

### **Retour en haut**

[Retour en haut](#plan)




---
---
---

## **5. Trafic SMB : Sécurisation et analyse** <a id="trafic-smb-securisation-et-analyse"></a>


### **Objectifs**

- **Comprendre les vulnérabilités du trafic SMB :**
  - Identifier les principales vulnérabilités associées au protocole SMB (Server Message Block), telles que l'exploitation d'anciennes versions comme SMBv1, les attaques de type "man-in-the-middle", et les faiblesses de sécurité non corrigées.
  - Explorer les risques spécifiques à l'utilisation de SMB dans des environnements professionnels, y compris les failles historiques comme EternalBlue, et les scénarios d'exfiltration de données via SMB.

- **Apprendre à sécuriser le trafic SMB :**
  - Mettre en œuvre des pratiques de sécurité robustes pour sécuriser SMB, notamment par la désactivation de SMBv1, la mise à jour régulière des systèmes, l'application des permissions strictes, et le chiffrement des communications SMB.
  - Intégrer ces pratiques dans les stratégies de gestion des risques et de conformité pour minimiser l'impact potentiel des attaques exploitant SMB.

---

### **Contenu**

#### **1. Introduction au protocole SMB**

- **Historique et évolution :**
  - SMB a été développé à l'origine par IBM dans les années 1980, puis popularisé par Microsoft pour le partage de fichiers et d'imprimantes dans les réseaux Windows. Son adoption généralisée en a fait une cible privilégiée pour les cyberattaques.
  - **Évolution des versions :**
    - **SMBv1 (1990s)** : La première version, toujours présente dans certains systèmes hérités, est vulnérable à plusieurs exploits majeurs. Par exemple, elle n'a pas de chiffrement natif, ce qui la rend sensible aux interceptions.
    - **SMBv2 (2006)** : Introduite avec Windows Vista, cette version améliore la sécurité et la performance, mais présente encore des failles si elle n'est pas correctement configurée.
    - **SMBv3 (2012)** : Introduite avec Windows 8 et Windows Server 2012, SMBv3 ajoute le chiffrement natif du trafic, ce qui améliore considérablement la sécurité des communications.

- **Fonctionnement du protocole :**
  - **Modèle client-serveur :** SMB fonctionne en permettant à un client de faire des requêtes au serveur pour accéder à des ressources partagées. Le serveur répond avec les données demandées ou exécute l'action requise (par exemple, ouvrir un fichier, lire des données).
  - **Ports utilisés :**
    - **Port 139 (NetBIOS over TCP/IP) :** Historiquement utilisé pour les services NetBIOS, maintenant considéré obsolète.
    - **Port 445 (SMB over TCP) :** Utilisé pour les communications SMB sans NetBIOS, c'est le port le plus courant pour SMB dans les environnements modernes.

- **Structures des messages SMB :**
  - Les communications SMB sont basées sur un ensemble de commandes structurées en paquets. Chaque paquet SMB contient une en-tête SMB (identifiant, taille, etc.) et une section de données qui varie en fonction de la commande.

#### **2. Techniques de sécurisation du SMB**

- **Désactivation de SMBv1 :**
  - **Pourquoi désactiver SMBv1 ?**
    - SMBv1 est vulnérable à des attaques comme **EternalBlue**, qui exploitent une faille dans le protocole pour exécuter du code malveillant à distance. Cela peut mener à des compromissions majeures, telles que des ransomware, comme **WannaCry**.
  - **Comment désactiver SMBv1 ?**
    - Utilisation de PowerShell pour désactiver SMBv1 sur les systèmes Windows :
      ```powershell
      Disable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"
      ```
    - Pour vérifier que SMBv1 est désactivé :
      ```powershell
      Get-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"
      ```
      - **Résultat attendu :** La commande devrait montrer que SMBv1 est désactivé (`State : Disabled`). Il est crucial de vérifier cette désactivation sur tous les systèmes pour réduire les risques d'exploitation.

- **Activation du chiffrement SMB :**
  - **Pourquoi chiffrer le trafic SMB ?**
    - Le chiffrement assure que même si le trafic est intercepté, les données restent protégées. Cela prévient les attaques de type "man-in-the-middle", où un attaquant pourrait intercepter et modifier les communications SMB.
  - **Configuration du chiffrement dans SMBv3 :**
    - Pour activer le chiffrement sur un partage SMB spécifique :
      ```powershell
      Set-SmbShare -Name "DataShare" -EncryptData $true
      ```
      - **Explication :** `DataShare` est le nom du partage. Le paramètre `EncryptData` à `true` garantit que toutes les communications utilisant ce partage sont chiffrées.
    - Vérification du statut de chiffrement :
      ```powershell
      Get-SmbShare -Name "DataShare" | Select-Object Name, EncryptData
      ```
      - **Résultat attendu :** La commande devrait retourner `EncryptData : True`, confirmant que le chiffrement est activé pour ce partage.

- **Utilisation des permissions et des ACL (Access Control Lists) :**
  - **Mise en place des permissions strictes :**
    - Limiter l'accès aux ressources SMB en utilisant des ACL pour définir précisément qui peut accéder à quoi.
    - Exemple : Accorder à un groupe spécifique (`Domain\Admins`) un accès en lecture seule à un partage sensible :
      ```powershell
      $Acl = Get-Acl "C:\CriticalData"
      $Ar = New-Object System.Security.AccessControl.FileSystemAccessRule("Domain\Admins","Read","Allow")
      $Acl.SetAccessRule($Ar)
      Set-Acl "C:\CriticalData" $Acl
      ```
      - **Explication :** Ce script ajuste les permissions du dossier `C:\CriticalData` pour que seuls les membres du groupe `Domain\Admins` puissent lire les fichiers, empêchant ainsi toute modification non autorisée.
  
  - **Audit des accès :**
    - Configurer l'audit pour surveiller l'accès aux partages sensibles et recevoir des alertes en cas de tentatives d'accès non autorisé.
    - Exemple de configuration d'audit via PowerShell :
      ```powershell
      AuditPol /set /subcategory:"Object Access" /failure:enable
      ```
      - **Explication :** Cette commande active l'audit des accès aux objets (comme les fichiers et dossiers), enregistrant les tentatives d'accès échouées dans le journal de sécurité.

#### **3. Analyse des attaques courantes sur SMB**

- **Exemple 1 : Exploit EternalBlue**
  - **Contexte :** EternalBlue est une vulnérabilité découverte dans SMBv1, utilisée par des attaques massives telles que le ransomware **WannaCry**. Elle permet l'exécution de code à distance, souvent sans intervention de l'utilisateur.
  - **Analyse avec Microsoft Message Analyzer :**
    - **Étape 1 :** Capturer le trafic réseau ciblant les ports 139 et 445.
    - **Étape 2 :** Utiliser des filtres pour isoler les paquets envoyés avec des charges suspectes (payloads).
    - **Étape 3 :** Examiner les signatures spécifiques des exploits connus, telles que les séquences d'octets utilisées par EternalBlue.
    - **Résultat :** Identification des tentatives d'exploitation et réponse rapide pour bloquer les adresses IP sources et appliquer des correctifs de sécurité.
  
- **Exemple 2 : Attaque "man-in-the-middle" sur SMB**
  - **Contexte :** Dans une attaque MITM sur SMB, un attaquant intercepte la communication entre un client et un serveur pour écouter ou manipuler les données transmises.
  - **Analyse avec Microsoft Message Analyzer :**
    - **Étape 1 :** Surveiller les connexions SMB sur les ports 445 pour des comportements anormaux, comme des retards inexplicables ou des interruptions de communication.
    - **Étape 2 :** Analyser les flux de trafic pour repérer les redirections ou les anomalies dans les adresses IP.
    - **Résultat :** Détection des attaques MITM en identifiant les réponses non attendues provenant d'une source tierce et mise en place de contre-mesures, comme le chiffrement et le blocage des IP suspectes.

- **Exemple 3 : Ransomware exploitant SMB**
  - **Contexte :** Des ransomwares tels que **WannaCry** exploitent SMB pour se propager rapidement dans un réseau, chiffrer des fichiers et demander une rançon en échange de la clé de déchiffrement.
  - **Analyse des symptômes :**
    - **Symptômes :** Augmentation du trafic SMB, accès massifs et répétés aux fichiers, création soudaine de fichiers chiffrés.
    - **Étapes d'analyse :**
      - Capturer le trafic SMB et identifier les tentatives répétées de modification de fichiers.
      - Utiliser des outils d'analyse de logs pour corréler les événements réseau avec des alertes d'antivirus ou d'autres systèmes de détection d'intrusion (IDS).
   

 - **Résultat :** Isolation rapide des systèmes infectés, déconnexion des partages SMB pour contenir l'infection, et analyse post-incident pour renforcer la sécurité.






[Retour en haut](#plan)

