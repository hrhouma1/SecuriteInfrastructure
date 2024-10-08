# Plan
# *Analyse des activités systèmes et réseaux*
## **Table des matières**

1. [Présentation de l’audit](#presentation-de-laudit)
2. [Audit avancé et PowerShell](#audit-avance-et-powershell)
3. [Analyse des journaux des événements](#analyse-des-journaux-des-evenements)
4. [Examen du trafic réseau avec Microsoft Message Analyzer](#examen-du-trafic-reseau-avec-microsoft-message-analyzer)
5. [Trafic SMB : Sécurisation et analyse](#trafic-smb-securisation-et-analyse)



```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```





# **1. Présentation de l’audit** <a id="presentation-de-laudit"></a>

## **1.1 Objectifs**

Cette section vise à introduire les principes fondamentaux de l’audit dans un environnement informatique, avec un accent particulier sur les systèmes Windows. L’objectif est de fournir une compréhension claire de la nature de l’audit, de son importance pour la sécurité et la performance des systèmes, ainsi que des outils intégrés dans Windows qui facilitent la mise en œuvre d’audits efficaces.

**Sous-objectifs :**
- **Définir l’audit informatique** : Acquérir une compréhension précise du concept d’audit dans le contexte des systèmes d’information, en mettant l'accent sur son application pratique pour la sécurité et la gestion des réseaux.
- **Justifier la nécessité de l’audit** : Identifier les raisons pour lesquelles un audit régulier est indispensable, notamment en matière de protection des données sensibles et de conformité aux régulations.
- **Explorer les outils d’audit sous Windows** : Se familiariser avec les principaux outils intégrés à Windows, tels que l'Observateur d'événements, les Paramètres d’audit des stratégies de groupe, et PowerShell, pour effectuer des audits approfondis.

## **1.2 Introduction aux concepts de l’audit**

### **Qu’est-ce qu’un audit ?**

L’audit informatique est un processus systématique et rigoureux de collecte, d’évaluation, et de vérification des informations générées par un système informatique. Ce processus permet de s'assurer que les politiques, les procédures, et les contrôles en place au sein d’une organisation sont non seulement respectés, mais aussi efficaces pour garantir la sécurité et l'intégrité du système.

#### **Analogie :**
Imaginons que votre système informatique soit comparable à une maison. Auditer ce système revient à inspecter minutieusement chaque aspect de la maison pour s'assurer que toutes les portes et fenêtres sont bien verrouillées, que les systèmes de sécurité fonctionnent correctement, et qu'il n'existe pas de failles pouvant être exploitées. Dans un contexte informatique, cela signifie vérifier et analyser les activités au sein des systèmes et des réseaux pour identifier les connexions des utilisateurs, les modifications apportées aux fichiers, ou encore déceler des tentatives d'accès non autorisé à des données sensibles.

### **Pourquoi est-ce important ?**

Tout comme il serait imprudent de ne jamais vérifier la sécurité de sa maison, négliger l’audit de votre système informatique peut entraîner des conséquences graves. Sans audit, des failles de sécurité ou des dysfonctionnements peuvent passer inaperçus, mettant en péril la sécurité des données et l'intégrité du système.

L’audit permet de :
- **Protéger les données sensibles** : En s’assurant que seules les personnes autorisées ont accès aux informations critiques, l’audit réduit le risque de violations de données.
- **Respecter les obligations légales** : De nombreuses régulations, comme le Règlement Général sur la Protection des Données (RGPD) en Europe, exigent que les entreprises maintiennent des journaux précis de leurs activités pour garantir la traçabilité et la conformité.
- **Améliorer la performance du système** : En identifiant et en corrigeant rapidement les anomalies ou les dysfonctionnements, l’audit contribue à maintenir une performance optimale du système et à éviter les pannes ou interruptions de service.

### **1.3 Importance de l’audit dans une infrastructure Microsoft**

Microsoft étant l’un des principaux fournisseurs de solutions informatiques pour les entreprises, il est essentiel de comprendre comment auditer efficacement les systèmes Windows pour garantir leur sécurité, leur conformité, et leur performance.

#### **Exemple concret :**
Dans une organisation où les employés se connectent quotidiennement à un réseau pour effectuer leurs tâches, l’absence d’audit pourrait empêcher la détection d’activités malveillantes, telles qu’un employé utilisant les informations d’identification d’un autre pour accéder à des fichiers confidentiels. De plus, un logiciel malveillant pourrait s’installer et modifier des fichiers critiques sans que cela ne soit détecté.

La mise en œuvre régulière d’audits permet de :
- **Surveiller les activités sur le système** : Identifier qui accède à quels fichiers, quelles modifications sont effectuées, et détecter toute tentative d'accès non autorisé.
- **Repérer les anomalies** : Identifier des activités inhabituelles, telles que des tentatives de connexion en dehors des heures de travail habituelles, des erreurs fréquentes, ou des comportements suspects.
- **Prévenir les incidents graves** : En détectant les problèmes avant qu’ils ne deviennent critiques, l’audit permet de prendre des mesures correctives rapides pour minimiser les risques de pannes ou de violations de sécurité.

### **1.4 Types d'audit et résumé**

#### **Les types d’audit :**
- **Audit de Sécurité** : Évalue les vulnérabilités d'un système et le niveau de protection contre les menaces externes et internes.
  - **Exemple :** Vérifier l’accès aux fichiers sensibles pour s'assurer que seules les personnes autorisées peuvent les consulter et les modifier.
- **Audit de Conformité** : Vérifie que les pratiques de l'organisation sont conformes aux lois, régulations, et politiques internes.
  - **Exemple :** Contrôler les configurations des systèmes pour s'assurer qu'elles respectent les normes ISO 27001 ou les réglementations telles que le GDPR.
- **Audit Opérationnel** : Évalue l'efficacité des processus internes pour optimiser la performance du système.
  - **Exemple :** Analyser les performances des serveurs pour identifier les goulets d'étranglement et améliorer la réactivité des services.

#### **Résumé :**
L’audit informatique est un outil stratégique indispensable pour assurer la sécurité, la conformité, et l'efficacité opérationnelle d’une organisation. Les systèmes Windows proposent plusieurs outils intégrés pour réaliser ces audits, chacun offrant des fonctionnalités spécifiques pour collecter, analyser, et sécuriser les informations critiques.

**Outils d’audit disponibles sous Windows :**
- **Event Viewer (Observateur d'événements)** : Un outil essentiel pour consulter et analyser les journaux des événements afin de détecter des anomalies ou des tentatives d’intrusion.
- **Group Policy Audit Settings (Paramètres d’audit des stratégies de groupe)** : Configurable pour auditer divers types d’événements, tels que les tentatives de connexion ou les accès aux fichiers sensibles.
- **PowerShell** : Un outil puissant permettant d’automatiser la collecte de données d’audit et de réaliser des audits avancés à l'aide de scripts personnalisés.

[Retour en haut](#plan)





```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```


# **2. Audit avancé et PowerShell** <a id="audit-avance-et-powershell"></a>

## **Objectifs**

Cette section est dédiée à l’approfondissement des techniques d’audit en utilisant PowerShell, un outil essentiel pour automatiser et optimiser les processus d’audit des systèmes et réseaux. Vous apprendrez à configurer et utiliser des cmdlets spécifiques pour l’audit, à concevoir des scripts avancés visant à automatiser ces tâches, et à intégrer ces audits dans un flux de travail DevOps, garantissant ainsi une surveillance continue et proactive des systèmes informatiques.

**Sous-objectifs :**
- **Maîtriser l’utilisation de PowerShell pour l’audit** : Acquérir les compétences nécessaires pour configurer, automatiser, et exécuter des audits détaillés en utilisant PowerShell.
- **Automatiser le processus d’audit** : Développer des scripts PowerShell qui automatisent la collecte et l’analyse des données d’audit, ainsi que la génération de rapports périodiques.
- **Intégrer l’audit dans une démarche DevOps** : Comprendre comment intégrer un audit continu dans un flux de travail DevOps afin de garantir une surveillance proactive des systèmes.

## **Contenu**

### **1. Introduction à l’audit avec PowerShell**

PowerShell est un outil d'administration en ligne de commande et un langage de script puissant, spécifiquement conçu pour faciliter la gestion des systèmes Windows. Sa flexibilité et sa capacité à automatiser des tâches répétitives en font un choix incontournable pour la réalisation d’audits détaillés. Cette section vous introduira aux raisons pour lesquelles PowerShell est essentiel dans le contexte de l’audit des systèmes, en montrant comment il peut être utilisé pour surveiller les événements de sécurité, collecter les journaux, et configurer les paramètres de sécurité au sein de votre infrastructure.

**Points clés :**
- **Automatisation** : PowerShell permet d'automatiser la majorité des aspects de la gestion d’un système Windows, rendant ainsi les audits plus précis et moins sujets à des erreurs humaines.
- **Flexibilité** : Grâce à PowerShell, les audits peuvent être personnalisés pour répondre à des besoins spécifiques, tels que la surveillance des connexions ou le suivi des modifications apportées aux fichiers critiques.
- **Intégration native** : En étant nativement intégré à l'écosystème Windows, PowerShell s'impose comme un outil de prédilection pour les administrateurs système souhaitant mettre en place des audits efficaces dans un environnement Microsoft.

### **2. Scripts PowerShell pour l’audit**

PowerShell offre une gamme étendue de cmdlets (commandlets) qui simplifient considérablement l’audit des systèmes Windows. Dans cette section, nous examinerons certaines des cmdlets de base et avancées que vous utiliserez pour auditer efficacement votre infrastructure.

#### **Cmdlets de base pour l’audit :**
- **Get-EventLog** : Cette cmdlet permet de récupérer les journaux d'événements à partir de systèmes locaux ou distants. C’est une commande essentielle pour l’audit, car elle permet d’accéder rapidement aux événements enregistrés par le système.
  - **Exemple :** `Get-EventLog -LogName Security -Newest 1000` récupère les 1000 derniers événements de sécurité du journal spécifié, fournissant ainsi un aperçu des activités récentes sur le système.
- **Get-WinEvent** : Cette cmdlet est plus flexible et performante que `Get-EventLog`, offrant la possibilité de récupérer des événements à partir de journaux personnalisés. Elle est particulièrement utile pour des audits nécessitant une granularité accrue.
  - **Exemple :** `Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624}` permet de récupérer les événements d’ouverture de session (ID 4624) à partir du journal de sécurité, facilitant ainsi le suivi des connexions utilisateur.
- **Get-AuditPolicy** : Cette cmdlet permet de consulter les stratégies d’audit actuellement configurées sur un système, offrant une vue d’ensemble des paramètres de sécurité en place.
  - **Exemple :** `Get-AuditPolicy -Category *` liste toutes les catégories de stratégie d’audit et leurs sous-catégories, aidant ainsi à identifier les domaines couverts par les politiques actuelles.
- **Set-AuditPolicy** : Cette cmdlet configure les stratégies d’audit pour qu’elles répondent aux exigences de sécurité spécifiques de votre organisation.
  - **Exemple :** `Set-AuditPolicy -Category Logon/Logoff -Success Enable -Failure Enable` configure l’audit pour capturer à la fois les succès et les échecs de connexion, fournissant des informations complètes sur les tentatives d’accès au système.

#### **Scripts avancés d’audit :**

- **Audit des modifications dans les groupes privilégiés** :
  - **Objectif :** Surveiller et enregistrer les modifications apportées aux groupes d’utilisateurs privilégiés, tels que les groupes Administrateurs ou Domain Admins, pour détecter toute altération non autorisée.
  - **Script exemple :**
    ```powershell
    $events = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4728,4732,4756} 
    foreach ($event in $events) {
        $message = $event.Message
        $timeCreated = $event.TimeCreated
        Write-Output "$timeCreated : $message"
    }
    ```
    - **Explication :** Ce script récupère les événements où des utilisateurs ont été ajoutés à des groupes privilégiés et les affiche avec un horodatage. Cela permet une surveillance en temps réel des modifications critiques, facilitant ainsi une réponse rapide en cas d’activité suspecte.

- **Automatisation de la génération de rapports d’audit** :
  - **Objectif :** Automatiser la création de rapports d’audit pour garantir une surveillance régulière et systématique des activités sur le système.
  - **Script exemple :**
    ```powershell
    $reportPath = "C:\AuditReports\AuditReport_$(Get-Date -Format 'yyyyMMdd').txt"
    $auditData = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624,4634} | Select-Object TimeCreated, Id, Message
    $auditData | Out-File $reportPath
    ```
    - **Explication :** Ce script génère un rapport des événements d’ouverture et de fermeture de session, qu’il sauvegarde dans un fichier texte daté. En centralisant les informations d’audit, il facilite l’analyse rétrospective et permet de maintenir un historique détaillé des activités du système.

### **3. Exemples pratiques d’audit avancé**

#### **Surveillance des tentatives d’accès non autorisées** :
- **Objectif :** Détecter les tentatives de connexion échouées et les verrouillages de comptes, afin de prévenir les accès non autorisés au système.
- **Script exemple :**
  ```powershell
  $failedLogons = Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4625} | Select-Object TimeCreated, Message
  $failedLogons | Where-Object {$_.Message -match "Account Name:\s+(\S+)" } | Sort-Object TimeCreated | Format-Table -AutoSize
  ```
  - **Explication :** Ce script identifie et compile les tentatives de connexion échouées en analysant les journaux de sécurité. Cette surveillance permet de repérer rapidement les comptes ciblés par des tentatives d’intrusion, offrant ainsi une couche supplémentaire de protection.

#### **Suivi des modifications sur des fichiers critiques** :
- **Objectif :** Surveiller l’accès et les modifications apportées aux fichiers critiques du système afin de prévenir toute altération non autorisée.
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
  - **Explication :** Ce script surveille un répertoire pour détecter toute modification de fichier, enregistre les changements avec un horodatage, et fournit une traçabilité détaillée des accès aux fichiers critiques. Il est particulièrement utile pour protéger les données sensibles contre les modifications non autorisées, en assurant une surveillance continue et en temps réel.

[Retour en haut](#plan)



```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```







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

```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```







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
- **Configuration** : Configurez les adaptateurs réseau pour la capture, paramétrez les filtres pour cibler les types de trafic pertinents, et sauvegardez les paramètres de capture pour une utilisation ultérieure.

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



```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

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
    $Ar = New-Object System.Security.AccessControl.FileSystemAccessRule("Domain\Admins","Read","Allow")
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

```
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```
