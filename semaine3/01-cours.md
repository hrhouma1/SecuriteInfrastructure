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

Cette section a pour but de vous initier aux principes fondamentaux de l’audit dans un environnement informatique, en particulier sous Windows. Vous comprendrez ce qu’est un audit, pourquoi il est indispensable pour la sécurité et la performance des systèmes, et découvrirez les outils natifs de Windows qui facilitent la réalisation d’audits.

**Sous-objectifs :**
- **Définir l’audit informatique** : Vous comprendrez la signification du terme "audit" dans le contexte informatique et comment il s'applique aux systèmes et réseaux.
- **Justifier la nécessité de l’audit** : Vous apprendrez à identifier les raisons pour lesquelles un audit régulier est crucial, notamment pour la sécurité des données et la conformité légale.
- **Explorer les outils d’audit sous Windows** : Vous vous familiariserez avec les principaux outils intégrés à Windows pour effectuer un audit, comme l'Observateur d'événements, les Paramètres d’audit des stratégies de groupe, et PowerShell.

## **1.2 Introduction aux concepts de l’audit**

### **Qu’est-ce qu’un audit ?**

Un audit informatique est un processus méthodique de collecte, d'évaluation, et de vérification des informations issues d'un système informatique. Il permet de s'assurer que les politiques, procédures, et contrôles en place dans une organisation sont efficaces, conformes aux normes, et assurent la sécurité du système.

#### **Analogie :**
Imaginez que votre système informatique est une maison. Auditer le système, c’est comme effectuer une inspection complète de la maison pour vérifier que toutes les portes et fenêtres sont bien verrouillées, que les systèmes de sécurité fonctionnent correctement, et qu'il n'y a pas de fuites ou de points faibles susceptibles d'être exploités.

Dans le cadre informatique, l'audit consiste à vérifier les activités sur vos systèmes et réseaux. Par exemple, cela inclut l’examen des connexions des utilisateurs, des modifications apportées aux fichiers, ou encore la détection d'éventuelles tentatives d'accès non autorisé à des données sensibles.

### **Pourquoi est-ce important ?**

Ne jamais vérifier votre maison reviendrait à ignorer les risques de laisser une porte ouverte ou de ne pas remarquer qu'un intrus a pénétré dans votre espace. De la même manière, ne pas auditer votre système informatique pourrait entraîner la découverte tardive de failles de sécurité ou de dysfonctionnements qui pourraient avoir des conséquences graves.

L'audit permet de :
- **Protéger les données sensibles** : En s'assurant que seules les personnes autorisées ont accès aux informations critiques.
- **Respecter les obligations légales** : De nombreuses régulations exigent que vous conserviez des traces des événements dans vos systèmes.
- **Améliorer la performance du système** : En détectant les dysfonctionnements ou les anomalies pour les corriger avant qu’ils n’affectent la productivité ou la sécurité.

### **1.3 Importance de l’audit dans une infrastructure Microsoft**

Microsoft est un fournisseur majeur de solutions informatiques pour les entreprises, avec une large adoption de systèmes Windows. Dans ce contexte, savoir comment auditer ces systèmes est essentiel pour assurer leur sécurité, leur conformité et leur performance.

#### **Exemple concret :**
Supposons que vous travaillez dans une entreprise où les employés se connectent quotidiennement à un réseau pour effectuer leur travail. Sans audit, vous pourriez ignorer qu'un employé utilise les informations de connexion d'un autre pour accéder à des fichiers auxquels il ne devrait pas avoir accès. De même, vous pourriez ne pas détecter l’installation d’un logiciel malveillant qui modifie des fichiers critiques.

L'audit régulier permet de :
- **Surveiller les activités sur le système** : Vous saurez qui accède à quels fichiers, quelles modifications sont apportées, et si des tentatives d'accès non autorisé ont lieu.
- **Repérer les anomalies** : Comme des tentatives de connexion en dehors des heures habituelles, des erreurs répétées, ou des comportements suspects.
- **Prévenir les incidents graves** : En détectant les problèmes avant qu’ils ne se transforment en pannes ou en violations de sécurité, vous pouvez intervenir rapidement et limiter les dégâts.

### **1.4 Types d'audit et résumé**

#### **Les types d’audit :**
- **Audit de Sécurité** : Évalue les vulnérabilités d'un système et son niveau de protection contre les menaces externes et internes.
  - **Exemple :** Vérification des accès aux fichiers sensibles pour s'assurer que seules les personnes autorisées peuvent y accéder.
- **Audit de Conformité** : Vérifie que les pratiques de l'organisation sont en adéquation avec les lois, régulations, et politiques internes.
  - **Exemple :** Contrôle des configurations pour s'assurer qu'elles respectent les normes ISO 27001 ou les réglementations GDPR.
- **Audit Opérationnel** : Évalue l'efficacité des processus internes pour optimiser les performances du système.
  - **Exemple :** Analyse des performances des serveurs pour identifier les goulets d'étranglement et améliorer la réactivité.

#### **Résumé :**
L'audit informatique est un outil stratégique pour la sécurité, la conformité et l'efficacité opérationnelle. Windows propose plusieurs outils intégrés pour réaliser ces audits, chacun ayant des fonctions spécifiques pour collecter, analyser, et sécuriser les informations critiques.

**Outils d’audit disponibles sous Windows :**
- **Event Viewer (Observateur d'événements)** : Permet de consulter et d'analyser les journaux des événements pour détecter des anomalies ou des tentatives d’intrusion.
- **Group Policy Audit Settings (Paramètres d’audit des stratégies de groupe)** : Configurable pour auditer divers types d’événements, tels que les tentatives de connexion, les accès aux fichiers, etc.
- **PowerShell** : Permet d’automatiser la collecte de données d’audit et d’effectuer des audits avancés grâce à des scripts personnalisés.

[Retour en haut](#plan)

---

# **2. Audit avancé et PowerShell** <a id="audit-avance-et-powershell"></a>

## **Objectifs**

Cette section approfondira les techniques d’audit en utilisant PowerShell, un outil puissant pour automatiser les tâches d’audit. Vous apprendrez à configurer des cmdlets spécifiques pour l’audit des systèmes et réseaux, à créer des scripts avancés pour automatiser ces tâches, et à intégrer l’audit dans un flux de travail DevOps pour garantir une surveillance continue.

**Sous-objectifs :**
- **Maîtriser PowerShell pour l’audit** : Apprendre à utiliser PowerShell pour configurer, automatiser, et exécuter des audits détaillés.
- **Automatiser l’audit** : Développer des scripts pour automatiser la collecte, l'analyse des données d’audit, et la génération de rapports.
- **Intégrer l’audit dans DevOps** : Savoir comment intégrer l’audit continu dans un flux de travail DevOps pour une surveillance proactive.

## **Contenu**

### **1. Introduction à l’audit avec PowerShell**

PowerShell est une interface en ligne de commande et un langage de script conçu pour faciliter l'administration des systèmes Windows. Il offre une flexibilité et une puissance accrues pour automatiser les tâches répétitives, y compris les audits. Vous découvrirez pourquoi PowerShell est un outil incontournable pour l'audit des systèmes et comment il peut être utilisé pour surveiller les événements de sécurité, collecter les journaux, et configurer les paramètres de sécurité sur l’ensemble de votre infrastructure.

**Points clés :**
- **Automatisation** : PowerShell permet d'automatiser presque tous les aspects d'un système Windows, rendant les audits plus efficaces et moins sujets à l'erreur humaine.
- **Flexibilité** : Avec PowerShell, vous pouvez personnaliser les audits pour répondre à des besoins spécifiques, comme la surveillance des connexions ou la vérification des modifications des fichiers critiques.
- **Intégration native** : PowerShell est intégré à Windows, ce qui facilite son adoption pour les administrateurs système dans un environnement Microsoft.

### **2. Scripts PowerShell pour l’audit**

PowerShell offre une variété de cmdlets (commandlets) qui simplifient l'audit des systèmes Windows. Voici quelques cmdlets de base et avancés que vous utiliserez pour auditer votre infrastructure :

#### **Cmdlets de base pour l’audit :**
- **Get-EventLog** : Cette cmdlet permet de récupérer les journaux d'événements à partir de systèmes locaux et distants.
  - **Exemple :** `Get-EventLog -LogName Security -Newest 1000` récupère les 1000 derniers événements de sécurité du journal spécifié.
- **Get-WinEvent** : Plus flexible et performant que `Get-EventLog`, cette cmdlet permet de récupérer les événements, y compris ceux des journaux personnalisés.
  - **Exemple :

** `Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624}` permet de récupérer les événements d’ouverture de session (ID 4624) à partir du journal de sécurité.
- **Get-AuditPolicy** : Cette cmdlet permet de consulter les stratégies d’audit actuelles configurées sur un système.
  - **Exemple :** `Get-AuditPolicy -Category *` liste toutes les catégories de stratégie d’audit et leurs sous-catégories.
- **Set-AuditPolicy** : Cette cmdlet configure les stratégies d’audit pour qu’elles répondent aux exigences de sécurité de votre organisation.
  - **Exemple :** `Set-AuditPolicy -Category Logon/Logoff -Success Enable -Failure Enable` permet d’auditer les succès et échecs de connexion.

#### **Scripts avancés d’audit :**

- **Audit des modifications dans les groupes privilégiés** :
  - **Objectif :** Surveiller les modifications apportées aux groupes d’utilisateurs privilégiés, comme les Administrateurs ou Domain Admins.
  - **Script exemple :**
    ```powershell
    $events = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4728,4732,4756} 
    foreach ($event in $events) {
        $message = $event.Message
        $timeCreated = $event.TimeCreated
        Write-Output "$timeCreated : $message"
    }
    ```
    - **Explication :** Ce script récupère les événements où des utilisateurs ont été ajoutés à des groupes privilégiés et les affiche avec un horodatage. Cela permet de surveiller les modifications sensibles en temps réel.

- **Automatisation de la génération de rapports d’audit** :
  - **Objectif :** Automatiser la création de rapports d’audit qui peuvent être examinés périodiquement pour assurer une surveillance continue.
  - **Script exemple :**
    ```powershell
    $reportPath = "C:\AuditReports\AuditReport_$(Get-Date -Format 'yyyyMMdd').txt"
    $auditData = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624,4634} | Select-Object TimeCreated, Id, Message
    $auditData | Out-File $reportPath
    ```
    - **Explication :** Ce script génère un rapport des événements d’ouverture et de fermeture de session et le sauvegarde dans un fichier texte daté. Cela permet de centraliser les informations d’audit pour une analyse ultérieure.

### **3. Exemples pratiques d’audit avancé**

#### **Surveillance des tentatives d’accès non autorisées** :
- **Objectif :** Détecter les tentatives de connexion échouées et les verrouillages de comptes pour prévenir les accès non autorisés.
- **Script exemple :**
  ```powershell
  $failedLogons = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4625} | Select-Object TimeCreated, Message
  $failedLogons | Where-Object {$_.Message -match "Account Name:\s+(\S+)" } | Sort-Object TimeCreated | Format-Table -AutoSize
  ```
  - **Explication :** Ce script identifie les tentatives de connexion échouées en analysant les journaux de sécurité, permettant ainsi de repérer les comptes ciblés par des tentatives d’intrusion.

#### **Suivi des modifications sur des fichiers critiques** :
- **Objectif :** Surveiller l’accès et les modifications des fichiers critiques du système pour prévenir les altérations non autorisées.
- **Script exemple :**
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
  - **Explication :** Ce script surveille un répertoire pour toute modification de fichier et enregistre les changements avec un horodatage. Il est particulièrement utile pour sécuriser les fichiers critiques contre les modifications non autorisées.

[Retour en haut](#plan)

---

# **3. Analyse des journaux des événements** <a id="analyse-des-journaux-des-evenements"></a>

## **Objectifs**

Cette section vous apprendra à analyser les journaux des événements pour identifier les anomalies et les comportements suspects. Vous apprendrez à accéder aux journaux des événements, à interpréter les données, et à utiliser les informations recueillies pour améliorer la sécurité et la performance de votre infrastructure.

**Sous-objectifs :**
- **Accéder aux journaux des événements** : Savoir comment accéder aux différents types de journaux sous Windows.
- **Analyser et interpréter les données** : Apprendre à identifier des schémas, des anomalies, et des comportements suspects à partir des journaux collectés.
- **Utiliser les journaux pour renforcer la sécurité** : Appliquer les résultats de l’analyse pour renforcer les politiques de sécurité et répondre aux incidents.

## **Contenu**

### **1. Introduction aux journaux des événements**

Les journaux des événements sous Windows sont une ressource précieuse pour surveiller les activités et diagnostiquer les problèmes. Ils enregistrent une large gamme d’activités, des tentatives de connexion aux erreurs système, en passant par les modifications apportées aux fichiers. Vous découvrirez comment accéder à ces journaux via l'Observateur d'événements et d’autres outils, et comment les configurer pour enregistrer les informations pertinentes à vos besoins.

### **2. Analyse des journaux des événements**

#### **Étape 1 : Accès aux journaux des événements**
- **Accéder aux journaux** : Utiliser l'Observateur d'événements pour consulter les journaux de sécurité, d’application, et système.
- **Configuration des paramètres d’audit** : Ajuster les paramètres d’audit pour enregistrer les événements critiques.

#### **Étape 2 : Identification des anomalies**
- **Analyse des schémas** : Détecter des schémas inhabituels, comme des connexions répétées en dehors des heures de bureau.
- **Recherche d’anomalies** : Utiliser des outils comme PowerShell pour extraire et analyser les événements spécifiques.

#### **Étape 3 : Réponse aux incidents**
- **Interprétation des données** : Analyser les données pour déterminer la cause d'un problème ou d'une anomalie.
- **Actions correctives** : Mettre en œuvre des mesures correctives basées sur les informations des journaux, comme le blocage d'un compte compromis ou le renforcement des règles de sécurité.

[Retour en haut](#plan)

---

# **4. Examen du trafic réseau avec Microsoft Message Analyzer** <a id="examen-du-trafic-reseau-avec-microsoft-message-analyzer"></a>

## **Objectifs**

Cette section vous guidera dans l’utilisation de Microsoft Message Analyzer pour capturer, analyser, et diagnostiquer le trafic réseau. Vous apprendrez à configurer l’outil pour une analyse approfondie du trafic, à identifier les problèmes de réseau courants, et à corréler les événements réseau avec les journaux système pour un diagnostic complet.

**Sous-objectifs :**
- **Maîtriser Microsoft Message Analyzer** : Apprendre à capturer et analyser le trafic réseau en temps réel ou à partir de fichiers de capture.
- **Diagnostiquer les problèmes réseau** : Identifier les sources de latence, les erreurs de communication, et les problèmes de connectivité.
- **Corréler les événements réseau avec les journaux système** : Utiliser les informations collectées pour diagnostiquer les causes profondes des problèmes.

## **Contenu**

### **1. Introduction à Microsoft Message Analyzer**

Microsoft Message Analyzer est un outil de capture et d’analyse de trafic réseau qui succède à Network Monitor. Il permet de capturer le trafic en temps réel, d'analyser les journaux d’événements, les traces, et les fichiers de log provenant de diverses sources. Vous découvrirez ses principales fonctionnalités et apprendrez à l’installer et le configurer pour capturer les types de trafic spécifiques qui vous intéressent.

#### **Présentation de l’outil :**
- **Fonctionnalités principales** :
  - **Capture du trafic en temps réel** : Analysez les données directement à partir des interfaces réseau actives.
  - **Importation de fichiers de capture existants** : Importez et analysez des fichiers .cap, .etl, .log pour un diagnostic post-incident.
  - **Analyse des protocoles réseau** : Examinez les protocoles courants comme TCP/IP, HTTP, SMB, etc.
  - **Filtrage et tri des données** : Concentrez-vous sur des événements spécifiques grâce aux filtres avancés.
  - **Outils de visualisation** : Utilisez des graphiques et des vues pour mieux comprendre le flux de trafic et les interactions réseau.

#### **Installation et configuration :**
- **Installation** : Téléchargez Microsoft Message Analyzer depuis le Centre de téléchargement Microsoft et suivez les instructions d'installation.
- **Configuration** : Configurez les adaptateurs réseau pour la capture, paramétrez les filtres pour cibler les types de trafic pertinents, et sauvegard

ez les paramètres de capture pour une utilisation ultérieure.

### **2. Études de cas d’analyse de trafic réseau**

Les études de cas suivantes vous montreront comment utiliser Microsoft Message Analyzer pour diagnostiquer différents problèmes de réseau :

#### **Cas pratique 1 : Dépannage des problèmes de latence réseau**
- **Objectif :** Identifier les sources de latence dans le réseau qui affectent la performance des applications.
- **Étapes :**
  1. **Capture du trafic** : Configurez Microsoft Message Analyzer pour capturer le trafic sur l’interface réseau affectée.
  2. **Filtrage** : Appliquez des filtres pour ne capturer que les paquets TCP ou HTTP liés aux services affectés par la latence.
  3. **Analyse** : Mesurez le temps de réponse et identifiez les goulots d'étranglement dans les communications.
  4. **Résolution** : Proposez des solutions basées sur les résultats, comme l'optimisation des routes réseau ou l'ajustement des configurations de serveur.

- **Exemple de filtre :**
  ```plaintext
  Tcp.DstPort == 80 && Tcp.Flags.Syn == 1
  ```
  - **Explication :** Ce filtre capture les paquets SYN du protocole TCP destinés au port 80 (HTTP), essentiels pour analyser les connexions web.

#### **Cas pratique 2 : Analyse des communications SMB (Server Message Block)**
- **Objectif :** Diagnostiquer les problèmes liés aux partages de fichiers sur le réseau via SMB.
- **Étapes :**
  1. **Capture** : Démarrez la capture en ciblant les paquets SMB.
  2. **Filtrage** : Isolez les paquets liés à SMB pour se concentrer sur ce protocole.
  3. **Analyse** : Identifiez les erreurs et les délais dans les transactions SMB.
  4. **Résolution** : Ajustez les configurations SMB pour améliorer les performances et la sécurité.

- **Exemple de filtre :**
  ```plaintext
  smb2
  ```
  - **Explication :** Ce filtre permet de capturer uniquement les paquets utilisant le protocole SMB2, souvent utilisés pour le partage de fichiers dans les environnements Windows modernes.

#### **Cas pratique 3 : Détection des attaques réseau**
- **Objectif :** Utiliser Microsoft Message Analyzer pour détecter et analyser une attaque réseau, telle qu'une attaque par déni de service (DDoS).
- **Étapes :**
  1. **Capture** : Configurez une capture en continu pour surveiller les tentatives de connexion suspectes.
  2. **Analyse** : Identifiez des modèles de trafic anormaux, comme des volumes élevés de requêtes provenant d'une seule source.
  3. **Résolution** : Proposez des mesures de sécurité, comme le blocage des adresses IP suspectes ou le durcissement des configurations de pare-feu.

- **Exemple de filtre :**
  ```plaintext
  ip.src == 192.168.1.100 && Tcp.DstPort == 443
  ```
  - **Explication :** Ce filtre capture tout le trafic provenant de l’adresse IP 192.168.1.100 à destination du port 443 (HTTPS), utile pour détecter une tentative d'attaque spécifique.

[Retour en haut](#plan)

---

# **5. Trafic SMB : Sécurisation et analyse** <a id="trafic-smb-securisation-et-analyse"></a>

## **Objectifs**

Dans cette section, vous apprendrez à identifier et sécuriser les vulnérabilités associées au protocole SMB (Server Message Block), un protocole clé pour le partage de fichiers et d'imprimantes sur les réseaux Windows. Vous découvrirez comment mettre en œuvre des pratiques de sécurité pour protéger SMB contre les exploits et les attaques, et comment analyser les attaques courantes pour renforcer la sécurité de votre réseau.

**Sous-objectifs :**
- **Comprendre les vulnérabilités du protocole SMB** : Identifier les risques associés à l’utilisation de SMB, en particulier avec des versions obsolètes comme SMBv1.
- **Apprendre à sécuriser SMB** : Mettre en œuvre des pratiques de sécurité telles que la désactivation de SMBv1, l’application des permissions strictes, et le chiffrement des communications SMB.
- **Analyser les attaques courantes sur SMB** : Étudier les attaques comme EternalBlue et apprendre à détecter et à répondre à ces menaces.

## **Contenu**

### **1. Introduction au protocole SMB**

SMB est un protocole réseau essentiel pour le partage de fichiers et d’imprimantes dans les environnements Windows. Cependant, son adoption généralisée en fait une cible fréquente pour les cyberattaques. Cette section couvre l’historique du protocole, ses différentes versions, et les raisons pour lesquelles il est crucial de le sécuriser.

#### **Historique et évolution :**
- **Origines de SMB** : Développé initialement par IBM dans les années 1980, puis popularisé par Microsoft, SMB est devenu un protocole standard pour le partage de ressources sur les réseaux locaux.
- **Évolution des versions** :
  - **SMBv1 (1990s)** : La première version de SMB, bien que fonctionnelle, présente de nombreuses vulnérabilités, notamment l'absence de chiffrement, rendant les données échangées facilement interceptables.
  - **SMBv2 (2006)** : Cette version introduit avec Windows Vista améliore la sécurité et la performance du protocole, mais reste vulnérable si mal configurée.
  - **SMBv3 (2012)** : Introduite avec Windows 8 et Windows Server 2012, SMBv3 ajoute le chiffrement natif du trafic, une fonctionnalité essentielle pour sécuriser les communications sur des réseaux non sécurisés.

#### **Fonctionnement du protocole :**
- **Modèle client-serveur** : SMB fonctionne selon un modèle client-serveur, où le client envoie des requêtes au serveur pour accéder à des ressources partagées (fichiers, imprimantes) et le serveur répond en exécutant l’action demandée.
- **Ports utilisés** :
  - **Port 139 (NetBIOS over TCP/IP)** : Historiquement utilisé pour les services NetBIOS, maintenant considéré obsolète et remplacé par des ports plus sécurisés.
  - **Port 445 (SMB over TCP)** : Principal port utilisé pour les communications SMB sans NetBIOS, et donc recommandé dans les environnements modernes.

- **Structures des messages SMB** : Chaque communication SMB est constituée de commandes structurées en paquets, comprenant une en-tête SMB (avec des informations telles que l'identifiant et la taille) et une section de données qui varie en fonction de la commande.

### **2. Techniques de sécurisation du SMB**

#### **Désactivation de SMBv1 :**
- **Pourquoi désactiver SMBv1 ?**
  - SMBv1 est vulnérable à des attaques majeures comme **EternalBlue**, qui exploitent des failles dans le protocole pour exécuter du code malveillant à distance. La désactivation de SMBv1 est donc cruciale pour éviter des attaques dévastatrices telles que le ransomware **WannaCry**.
- **Comment désactiver SMBv1 ?**
  - **Utilisation de PowerShell** :
    ```powershell
    Disable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"
    ```
    - **Explication :** Cette commande désactive le protocole SMBv1 sur le système, empêchant ainsi son utilisation pour des communications réseau.
  - **Vérification de la désactivation** :
    ```powershell
    Get-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol"
    ```
    - **Résultat attendu :** La commande doit montrer que SMBv1 est désactivé (`State : Disabled`), confirmant ainsi que le système est protégé contre les exploits associés à SMBv1.

#### **Activation du chiffrement SMB :**
- **Pourquoi chiffrer le trafic SMB ?**
  - Le chiffrement du trafic SMB est essentiel pour protéger les données échangées contre les attaques de type "man-in-the-middle" (MITM), où un attaquant pourrait intercepter et altérer les communications non chiffrées.
- **Configuration du chiffrement dans SMBv3 :**
  - **Activer le chiffrement sur un partage SMB spécifique** :
    ```powershell
    Set-SmbShare -Name "DataShare" -EncryptData $true
    ```
    - **Explication :** Cette commande active le chiffrement pour le partage nommé `DataShare`, garantissant que toutes les communications utilisant ce partage sont sécurisées.
  - **Vérification du statut de chiffrement** :
    ```powershell
    Get-SmbShare -Name "DataShare" | Select-Object Name, EncryptData
    ```
    - **Résultat attendu :** La commande doit retourner `EncryptData : True`, confirmant que le chiffrement est activé pour ce partage.

#### **Utilisation des permissions et des ACL (Access Control Lists) :**
- **Mise en place des permissions strictes** :
  - Limitez l'accès aux ressources SMB en utilisant des ACL pour définir précisément qui peut accéder à quoi. Cela permet de protéger les données sensibles en restreignant les droits d'accès aux utilisateurs autorisés uniquement.
  - **Exemple de configuration** :
    ```powershell
    $Acl = Get-Acl "C:\CriticalData"
    $Ar = New-Object System

.Security.AccessControl.FileSystemAccessRule("Domain\Admins","Read","Allow")
    $Acl.SetAccessRule($Ar)
    Set-Acl "C:\CriticalData" $Acl
    ```
    - **Explication :** Ce script modifie les permissions du dossier `C:\CriticalData` pour que seuls les membres du groupe `Domain\Admins` puissent lire les fichiers, empêchant ainsi toute modification non autorisée.
  
- **Audit des accès** :
  - Configurez l'audit pour surveiller l'accès aux partages sensibles et recevez des alertes en cas de tentatives d'accès non autorisé. Cela permet de renforcer la sécurité en détectant les activités suspectes et en réagissant rapidement.
  - **Exemple de configuration d'audit via PowerShell** :
    ```powershell
    AuditPol /set /subcategory:"Object Access" /failure:enable
    ```
    - **Explication :** Cette commande active l'audit des accès aux objets, tels que les fichiers et dossiers, en enregistrant les tentatives d'accès échouées dans le journal de sécurité, facilitant ainsi la détection des tentatives d'intrusion.

### **3. Analyse des attaques courantes sur SMB**

#### **Exemple 1 : Exploit EternalBlue**
- **Contexte :** EternalBlue est une vulnérabilité dans SMBv1 qui a été exploitée par des attaques massives comme le ransomware **WannaCry**. Cette faille permet l'exécution de code malveillant à distance sans intervention de l'utilisateur, entraînant des compromissions majeures du système.
- **Analyse avec Microsoft Message Analyzer** :
  - **Étape 1 :** Capturez le trafic réseau ciblant les ports 139 et 445, utilisés par SMB.
  - **Étape 2 :** Utilisez des filtres pour isoler les paquets envoyés avec des charges suspectes (payloads).
  - **Étape 3 :** Examinez les signatures spécifiques des exploits connus, telles que les séquences d'octets utilisées par EternalBlue.
  - **Résultat :** Identification des tentatives d'exploitation et réponse rapide pour bloquer les adresses IP sources et appliquer des correctifs de sécurité.

#### **Exemple 2 : Attaque "man-in-the-middle" sur SMB**
- **Contexte :** Dans une attaque MITM sur SMB, un attaquant intercepte la communication entre un client et un serveur pour écouter ou manipuler les données transmises.
- **Analyse avec Microsoft Message Analyzer** :
  - **Étape 1 :** Surveillez les connexions SMB sur les ports 445 pour des comportements anormaux, tels que des retards inexplicables ou des interruptions de communication.
  - **Étape 2 :** Analysez les flux de trafic pour repérer les redirections ou les anomalies dans les adresses IP.
  - **Résultat :** Détection des attaques MITM en identifiant les réponses non attendues provenant d'une source tierce et mise en place de contre-mesures, comme le chiffrement et le blocage des IP suspectes.

#### **Exemple 3 : Ransomware exploitant SMB**
- **Contexte :** Des ransomwares tels que **WannaCry** exploitent SMB pour se propager rapidement dans un réseau, chiffrer des fichiers et demander une rançon en échange de la clé de déchiffrement.
- **Analyse des symptômes :**
  - **Symptômes :** Augmentation du trafic SMB, accès massifs et répétés aux fichiers, création soudaine de fichiers chiffrés.
  - **Étapes d'analyse** :
    - Capturez le trafic SMB et identifiez les tentatives répétées de modification de fichiers.
    - Utilisez des outils d'analyse de logs pour corréler les événements réseau avec des alertes d'antivirus ou d'autres systèmes de détection d'intrusion (IDS).
    - **Résultat :** Isolation rapide des systèmes infectés, déconnexion des partages SMB pour contenir l'infection, et analyse post-incident pour renforcer la sécurité.

[Retour en haut](#plan)
