# Énoncé du Laboratoire

# A. Besoin du laboratoire
Pour accomplir ce laboratoire, vous aurez besoin des composants matériels et logiciels suivants :

- **Serveur SRV**:
  - **OS**: Windows Server 2016/2019 Datacenter
  - **Nom NetBIOS**: SRV
  - **NIC**: 1 interface, @IP à déterminer selon le plan d’adressage du réseau (B)
  - **Domaine**: Intégré à Contoso.net
  - **Fonction**: machine contenant un partage SMB et centralisant les journaux d’évènement des autres machines.

- **Serveur Router**:
  - **OS**: Windows Server 2016/2019 Datacenter
  - **Nom NetBIOS**: Router (non nécessairement intégré au domaine)
  - **NIC**: 3 interfaces, @IP à déterminer selon le plan d’adressage des 3 réseaux : (A), (B) et (E)
  - **Fonction**: routage entre les réseaux et partage de connexion Internet.

- **Serveur DC (Domain Controller)**:
  - **OS**: Windows Server 2016/2019 Datacenter
  - **Nom NetBIOS**: DC
  - **NIC**: 1 interface, @IP à déterminer selon le plan d’adressage du réseau (A)
  - **Fonction**: Contrôleur de domaine pour Contoso.net.

- **Client CLI**:
  - **OS**: Windows 10 Professionnel
  - **Nom NetBIOS**: CLI
  - **NIC**: 1 interface, @IP à déterminer selon le plan d’adressage du réseau (A)
  - **Domaine**: Joint à Contoso.net

# B. Travail demandé

# Première partie : Adressage et implantation et configuration des rôles (20 pts)
1. **Subnetting**: Procédez au calcul du Subnetting pour distribuer l’adresse `10.11.12.0/?` pour vos deux réseaux (A) et (B). Choisissez un masque de sous-réseau optimal pour chacun des sous réseaux. Remplissez le tableau d’adressage des sous réseaux.
2. **Opérabilité des machines**:
   - **CLI**: Capturez des écrans montrant son intégration au domaine, la communication avec SRV (ping), et l'accès à Internet.
   - **DC**: Capturez des écrans montrant que le conteneur Computer contient les machines intégrées au domaine.
   - **Router**: Capturez des écrans montrant l'installation et la configuration du rôle de routage ainsi que le partage de connexion Internet.
   - **SRV**: Capturez un écran montrant son accès à Internet.

# Deuxième partie : Analyse du trafic lié au partage SMB (40 pts)
1. **Installation et configuration de Microsoft Message Analyzer** sur SRV.
2. **Capture et analyse du trafic**:
   - **Non chiffré**: Créez un fichier `MonSecret.txt` sur CLI et copiez-le vers `\\SRV\Labo1`. Capturez ce trafic sur SRV.
   - **Chiffré avec IPsec**: Activez IPsec sur DC, forcez les mises à jour avec `GPUPDATE`, puis capturez le trafic chiffré.
   - **Test du chiffrement SMB 3.1.1**: Configurez et testez le chiffrement SMB en créant un partage chiffré avec SMB sur SRV.

# Troisième partie : Transfert des journaux d’événements (30 pts)
1. **Configuration des sources d'événements** sur DC et CLI.
2. **Audit et collecte des journaux**:
   - **Configuration**: Activez WinRM, définissez les membres du groupe local Administrateurs, configurez SRV comme collecteur d'événements.
   - **Tests**: Activez l'audit de création des utilisateurs sur DC, l'audit d'accès sur CLI, et testez la présence des événements sur le journal du collecteur SRV.

# Consignes générales
- Réalisation individuelle ou en équipe de deux étudiants.
- Chaque capture d'écran doit être commentée et numérotée.
- Respectez les noms des machines et du domaine.
- Pénalité de retard de 10 % par jour.
- Date limite : 19 septembre 2024 avant 23h59.


----------------
----------------
----------------
# 🌐 Partie 1: Création d'un Domaine et Configuration des GPO
----------------
----------------
----------------


### 🖥️ Création d'un Contrôleur de Domaine (DC)

Pour transformer un serveur Windows en un Contrôleur de Domaine, exécutez les commandes suivantes en PowerShell avec des droits d'administrateur. Assurez-vous que le serveur est correctement configuré avec un nom statique et une adresse IP avant de promouvoir le serveur à un DC.

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
Import-Module ADDSDeployment
Install-ADDSForest -DomainName "Contoso.net" -InstallDNS -Force
```

🔍 **Explication**:
- `Install-WindowsFeature AD-Domain-Services`: Installe les services AD DS nécessaires.
- `Import-Module ADDSDeployment`: Charge le module nécessaire pour déployer AD DS.
- `Install-ADDSForest`: Crée une nouvelle forêt AD avec le domaine spécifié `Contoso.net`.

### 🛠️ Gestion des Objets de Stratégie de Groupe (GPO)

Pour créer et gérer des GPO avec PowerShell, utilisez les commandes suivantes :

```powershell
# Importer le module de gestion AD et GPO
Import-Module GroupPolicy

# Créer un nouveau GPO
$gpo = New-GPO -Name "SecuritySettings" -Comment "GPO for Security Enhancements"

# Lier le GPO à une unité organisationnelle (OU)
New-GPLink -Name "SecuritySettings" -Target "ou=Computers,dc=Contoso,dc=net"

# Configurer des paramètres spécifiques dans le GPO (exemple pour IPsec)
Set-GPRegistryValue -Name "SecuritySettings" -Key "HKLM\Software\Policies\Microsoft\Windows\IPSec\Policy\Local" -ValueName "EnableIPSec" -Type DWord -Value 1
```

🔍 **Explication**:
- `New-GPO`: Crée un nouvel objet de stratégie de groupe.
- `New-GPLink`: Lie le GPO à une unité organisationnelle spécifique.
- `Set-GPRegistryValue`: Configure des paramètres spécifiques au sein du GPO, par exemple pour activer IPsec.

### 🌐 Assignation d'Adresses IP Statiques avec PowerShell

Pour configurer une adresse IP statique sur un serveur Windows :

```powershell
# Obtenir l'index de l'interface réseau souhaitée
Get-NetAdapter

# Configurer une adresse IP statique
New-NetIPAddress -InterfaceIndex 12 -IPAddress 10.11.12.2 -PrefixLength 24 -DefaultGateway 10.11.12.1

# Configurer le serveur DNS
Set-DnsClientServerAddress -InterfaceIndex 12 -ServerAddresses 10.11.12.100
```

🔍 **Explication**:
- `Get-NetAdapter`: Affiche les interfaces réseau disponibles.
- `New-NetIPAddress`: Configure une adresse IP statique sur l'interface choisie.
- `Set-DnsClientServerAddress`: Définit l'adresse du serveur DNS pour l'interface spécifiée.

---

## 🔀 Schéma en ASCII de la Configuration du Réseau

```plaintext
Internet
    |
[Router]----[DC]
    |         |
[SRV]------[CLI]
```

**Légende**:
- **Router**: Routeur avec trois interfaces vers Internet, DC, et SRV.
- **DC**: Contrôleur de domaine avec connexion à Router et CLI.
- **SRV**: Serveur de fichiers avec connexion à Router.
- **CLI**: Client Windows connecté à DC et potentiellement à SRV.

---

📝 **Conseils pour les Étudiants**:
- 📖 **Documentez** chaque étape de votre processus pour mieux comprendre les configurations.
- 🛡️ **Pratiquez dans un environnement de test** pour éviter des erreurs dans un environnement de production.
- 🔄 **Vérifiez régulièrement** vos configurations pour vous assurer que tout fonctionne comme prévu.




----------------
----------------
----------------
# 🌐 Partie 1 (suite) :  Configuration Initiale de l'Infrastructure Réseau
----------------
----------------
----------------

----------
# Première Partie: Adressage et Implantation et Configuration des Rôles
----------

#### Calcul du Subnetting et Configuration des Adresses IP

Pour structurer efficacement votre réseau, vous allez diviser l'adresse `10.11.12.0` en deux sous-réseaux distincts pour accommoder les besoins spécifiques des deux groupes de machines dans l'environnement de laboratoire. 

##### Choix du masque de sous-réseau
- Pour le réseau (A), qui contient jusqu'à 10 machines, un masque de `/28` (255.255.255.240) offre 16 adresses IP, dont 14 sont utilisables (suffisantes pour les besoins actuels tout en laissant une petite marge pour l'expansion).
- Pour le réseau (B), qui doit supporter jusqu'à 12 machines, un masque de `/28` est également utilisé pour fournir 16 adresses IP, dont 14 sont utilisables. Ceci est choisi pour maintenir la cohérence et simplifier la gestion du réseau.

#### Tableau d’Adressage des Sous-Réseaux

| Réseau | NetID        | Première Adresse Utilisable | Dernière Adresse Utilisable | Masque de Sous-Réseau | Préfixe CIDR |
|--------|--------------|-----------------------------|----------------------------|-----------------------|--------------|
| A      | 10.11.12.0   | 10.11.12.1                  | 10.11.12.14                | 255.255.255.240       | /28          |
| B      | 10.11.12.16  | 10.11.12.17                 | 10.11.12.30                | 255.255.255.240       | /28          |

### Configuration des Serveurs et Clients

#### Configuration de Base pour Chaque Serveur et Client

Les étapes suivantes décrivent les commandes de configuration initiale pour les machines dans chaque réseau. Ces commandes préparent les machines pour l'intégration au domaine et la communication entre elles.

#### 1. Configuration de l'interface réseau

```powershell
# Exemple de commande pour configurer une adresse IP statique pour le serveur SRV
Get-NetAdapter | Where-Object { $_.Status -eq "Up" } | ForEach-Object {
    New-NetIPAddress -InterfaceAlias $_.Name -IPAddress 10.11.12.2 -PrefixLength 28 -DefaultGateway 10.11.12.1
    Set-DnsClientServerAddress -InterfaceAlias $_.Name -ServerAddresses 10.11.12.2
}
```

#### 2. Installation des rôles et fonctionnalités nécessaires

```powershell
# Installation des fonctionnalités pour le serveur DC (Active Directory Domain Services)
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
Import-Module ADDSDeployment
Install-ADDSForest -DomainName "Contoso.net" -InstallDNS -Force
```

#### 3. Configuration du routage (pour le serveur Router)

```powershell
# Activation du routage et accès à distance
Install-WindowsFeature Routing -IncludeManagementTools
Install-WindowsFeature RSAT-RemoteAccess-Mgmt
# Configurer les protocoles de routage ici
```

#### 4. Vérification des configurations

Pour assurer que toutes les machines sont correctement configurées et peuvent communiquer entre elles, utilisez les commandes de vérification suivantes :

```powershell
# Vérifier l'adressage IP
Get-NetIPAddress -AddressFamily IPv4

# Tester la connectivité
Test-NetConnection -ComputerName DC -CommonTCPPort ADWS
```

### Conseils d'Utilisation et Bonnes Pratiques

- **Documentation**: Assurez-vous de documenter chaque modification apportée à la configuration réseau pour faciliter le dépannage et la maintenance.
- **Sécurité**: Toujours vérifier les configurations de sécurité et mettre en place des mesures pour protéger les données circulant sur le réseau.
- **Mises à jour régulières**: Maintenir les systèmes à jour avec les derniers patches de sécurité pour protéger contre les vulnérabilités.







----------

# Deuxième Partie: Vérification de l'Opérabilité des Machines
----------

Cette partie du guide fournit des instructions détaillées pour vérifier l'opérabilité de chaque machine configurée dans l'environnement de laboratoire à l'aide d'outils en ligne de commande, substituant la nécessité de captures d'écran par des vérifications directes et des tests de connectivité.

### 1. Vérification de l'Intégration au Domaine et de la Connectivité

#### Client CLI

**Vérifier l'intégration au domaine :**
Pour confirmer que le client CLI est bien intégré au domaine `Contoso.net`, exécutez la commande suivante :

```powershell
# Vérifier l'appartenance au domaine
(Get-WmiObject -Class Win32_ComputerSystem).Domain
```

Cette commande retournera le nom du domaine auquel l'ordinateur est connecté.

**Tester la connectivité avec le serveur SRV :**
Utilisez `ping` pour confirmer la communication réseau :

```cmd
# Ping SRV pour tester la connectivité
ping srv.contoso.net
```

**Vérifier l'accès à Internet :**
Testez la connectivité à un service externe pour confirmer l'accès à Internet :

```cmd
# Ping Google DNS
ping 8.8.8.8
```

### 2. Serveur DC

**Vérifier les machines intégrées au domaine :**
Pour afficher toutes les machines intégrées au domaine depuis le DC, utilisez la commande suivante :

```powershell
# Lister tous les ordinateurs du domaine
Get-ADComputer -Filter *
```

Cette commande nécessite que le module Active Directory pour PowerShell soit installé et que vous ayez des privilèges administratifs sur le domaine.

### 3. Serveur Router

**Vérifier l'installation et la configuration du rôle de routage :**
Confirmez que les services de routage sont opérationnels avec la commande :

```powershell
# Vérifier l'état du service de routage
Get-Service RemoteAccess
```

**Vérifier le partage de connexion Internet :**
Pour s'assurer que le routage IP est activé, utilisez :

```cmd
# Voir les paramètres de routage IP
netsh interface ipv4 show config
```

### 4. Serveur SRV

**Vérifier l'accès à Internet :**
Pour tester l'accès à Internet depuis SRV, utilisez `ping` comme suit :

```cmd
# Ping Google DNS pour tester l'accès Internet
ping 8.8.8.8
```

## Conseils d'Utilisation et Bonnes Pratiques - partie 1

- **Exécution régulière** : Exécutez régulièrement ces commandes pour surveiller la santé du réseau et des machines.
- **Scripts** : Automatisez ces tests avec des scripts PowerShell ou des batchs CMD pour faciliter la surveillance continue.
- **Documentation** : Documentez les résultats de ces tests pour maintenir un registre de l'état du réseau et aider à identifier les problèmes de connectivité historiques ou récurrents.


## Conseils d'Utilisation et Bonnes Pratiques - partie 2

Pour assurer la maintenance et la surveillance efficaces de votre environnement réseau, voici des conseils pratiques que vous pouvez suivre :

### 1. **Exécution Régulière**

- **Planification**: Configurez des tâches planifiées pour exécuter automatiquement les commandes de vérification à intervalles réguliers. Cela permet de détecter rapidement les problèmes potentiels et de s'assurer que tous les systèmes fonctionnent correctement.

  **Exemple de mise en place avec PowerShell**:
  ```powershell
  # Créer une tâche planifiée pour vérifier la connectivité réseau toutes les heures
  $Action = New-ScheduledTaskAction -Execute 'PowerShell.exe' -Argument '-NoProfile -WindowStyle Hidden -Command "Test-Connection google.com"'
  $Trigger = New-ScheduledTaskTrigger -At 0:00 -Once -RepetitionInterval (New-TimeSpan -Hours 1)
  $Principal = New-ScheduledTaskPrincipal -UserId "SYSTEM" -LogonType ServiceAccount
  $Settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries

  Register-ScheduledTask -TaskName "CheckNetworkConnectivity" -Action $Action -Trigger $Trigger -Principal $Principal -Settings $Settings
  ```

### 2. **Scripts**

- **Automatisation**: Utilisez des scripts pour automatiser les tests de réseau et les tâches de maintenance. Les scripts PowerShell ou CMD peuvent être conçus pour collecter et analyser les données de manière automatisée, réduisant le besoin d'intervention manuelle et accélérant la résolution des problèmes.

  **Exemple de script PowerShell pour vérifier la connectivité de plusieurs serveurs**:
  ```powershell
  $servers = @("srv.contoso.net", "dc.contoso.net", "cli.contoso.net")
  foreach ($server in $servers) {
      $result = Test-Connection -ComputerName $server -Count 1 -Quiet
      if ($result -eq $true) {
          Write-Output "Connectivity to $server is up."
      } else {
          Write-Output "Connectivity to $server is down."
      }
  }
  ```

### 3. **Documentation**

- **Enregistrement des Résultats**: Maintenez un registre détaillé des résultats de vos tests et analyses. Ce registre peut être utilisé pour suivre les performances du réseau au fil du temps et identifier les tendances ou les problèmes récurrents.

  **Conseils pour une bonne documentation**:
  - Utilisez des outils comme ELK Stack (Elasticsearch, Logstash, Kibana) ou Prometheus avec Grafana pour collecter, stocker et visualiser les données de manière structurée.
  - Conservez des logs détaillés des événements et incidents pour faciliter le diagnostic en cas de problèmes.

  **Exemple simple de stockage des résultats dans un fichier log**:
  ```powershell
  $logPath = "C:\NetworkLogs\connectivity_log.txt"
  $date = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
  $servers = @("srv.contoso.net", "dc.contoso.net", "cli.contoso.net")
  foreach ($server in $servers) {
      $result = Test-Connection -ComputerName $server -Count 1 -Quiet
      $logEntry = "$date: Connectivity to $server is $(if ($result) {'up'} else {'down'})"
      Add-Content -Path $logPath -Value $logEntry
  }
  ```

En suivant ces conseils, vous pouvez maintenir un réseau robuste et réactif, capable de gérer les exigences dynamiques des utilisateurs et des applications modernes tout en minimisant les temps d'arrêt et les perturbations.











----------------
----------------
----------------
# 🌐 Partie 2 - Analyse du Trafic Lié au Partage SMB
----------------
----------------
----------------



Cette section du laboratoire est consacrée à l'analyse du trafic SMB, tant non chiffré que chiffré, pour évaluer la conformité et la sécurité du partage de fichiers sur le réseau. Voici les étapes détaillées pour configurer, capturer et analyser ce trafic.

### 1. Installation et Configuration de Microsoft Message Analyzer sur SRV

**Microsoft Message Analyzer** n'est plus disponible à partir d'octobre 2019, donc pour les besoins de ce README, nous considérerons un outil alternatif comme Wireshark ou similaire si vous utilisez encore cet outil dans un environnement contrôlé. Voici comment vous pourriez procéder avec un outil générique d'analyse de réseau :

```powershell
# Exemple d'installation de Wireshark via Chocolatey (un gestionnaire de paquets pour Windows)
choco install wireshark
```

Assurez-vous que l'outil est configuré pour capturer les trafics sur l'interface réseau connectée au réseau interne où le partage SMB est accessible.

### 2. Capture et Analyse du Trafic

#### Trafic Non Chiffré

**Créer et copier un fichier sur CLI**:

1. **Sur le client CLI**, créez un fichier texte pour le test:
   ```cmd
   echo "This is a secret file." > MonSecret.txt
   ```

2. **Copiez le fichier sur le partage SMB sur SRV**:
   ```cmd
   copy MonSecret.txt \\SRV\Labo1\
   ```

3. **Sur SRV, utilisez l'outil d'analyse réseau pour capturer le trafic pendant la copie**:
   - Lancez la capture avant de commencer la copie du fichier.
   - Filtrez le trafic par adresse IP de CLI et par protocole SMB/SMB2.

**Analyse du trafic**:
   - Observez les paquets SMB pour détecter des transmissions en clair du contenu ou des informations de fichier.

#### Trafic Chiffré avec IPsec

1. **Activez IPsec sur DC**:
   ```powershell
   # Configuration d'une règle IPsec pour chiffrement SMB
   Set-NetIPsecRule -Name "SMBEncryption" -Enabled True
   ```

2. **Forcez les mises à jour de politique de groupe sur les machines concernées**:
   ```cmd
   gpupdate /force
   ```

3. **Capturez le trafic sur SRV lors d'une nouvelle copie de fichier**:
   - Assurez-vous que la capture est active pendant que le fichier est de nouveau copié.
   - Le trafic doit maintenant être chiffré, ce qui se manifeste par des paquets ESP (Encapsulating Security Payload).

#### Test du Chiffrement SMB 3.1.1

1. **Configurez le partage SMB pour utiliser le chiffrement sur SRV**:
   ```powershell
   Set-SmbServerConfiguration -EncryptData $true -Force
   ```

2. **Testez la copie de fichiers sur ce partage**:
   - Copiez `MonSecret.txt` sur `\\SRV\Labo1` une fois que le chiffrement est activé.
   - Capturez et analysez le trafic pour vérifier que les paquets SMB sont chiffrés (le trafic SMB devrait être indiqué comme chiffré dans les détails des paquets SMB2).

### Conseils d'Utilisation et Bonnes Pratiques

- **Sécurisation des Tests**: Assurez-vous que les tests de sécurité comme ceux avec IPsec ou le chiffrement SMB 3.1.1 ne perturbent pas les opérations régulières. Effectuez ces tests dans un environnement de laboratoire ou pendant des périodes creuses.
- **Documentation Rigoureuse**: Documentez chaque étape du processus, y compris les configurations, les commandes exécutées, et les résultats des captures de trafic. Cela est crucial pour la validation des politiques de sécurité et l'audit.
- **Formation Continue**: Assurez-vous que tous les utilisateurs et administrateurs impliqués dans la gestion de l'infrastructure SMB sont formés sur les implications de sécurité des configurations SMB et IPsec.



----------------
----------------
----------------
# 🌐 Partie 3 - Correction de la Troisième Partie: Transfert des Journaux d’Événements
----------------
----------------
----------------

Cette section fournit les étapes détaillées pour la configuration des sources d'événements, l'audit, et la collecte des journaux dans un environnement réseau simulé, conformément aux spécifications du laboratoire.

# Configuration des Sources d'Événements sur DC et CLI

1. **Activez WinRM sur DC et CLI**:
   WinRM (Windows Remote Management) est nécessaire pour la communication à distance et la gestion des événements. Pour l'activer, exécutez la commande suivante sur les machines DC et CLI :

   ```powershell
   # Activer WinRM
   Enable-PSRemoting -Force
   ```

2. **Définissez les Membres du Groupe Local Administrateurs sur DC et CLI**:
   Assurez-vous que les comptes utilisés pour la collecte des événements ont des droits administratifs appropriés.

   ```powershell
   # Ajouter un utilisateur au groupe Administrateurs
   Add-LocalGroupMember -Group "Administrators" -Member "DOMAIN\User"
   ```

3. **Configurez SRV comme Collecteur d'Événements**:
   SRV doit être configuré pour collecter les journaux d'événements des autres machines (DC et CLI).

   ```powershell
   # Installer les fonctionnalités nécessaires pour la collecte d'événements
   Install-WindowsFeature -Name "Windows-Event-Collector"

   # Configurer SRV en tant que collecteur
   wecutil qc /q
   ```

# Audit et Collecte des Journaux

1. **Activez l'Audit de Création des Utilisateurs sur DC**:
   Pour suivre la création des utilisateurs, vous devez activer l'audit des événements de sécurité liés à la création des comptes.

   ```powershell
   # Activer l'audit de création des utilisateurs via GPO ou localement
   Auditpol.exe /set /subcategory:"User Account Management" /success:enable /failure:enable
   ```

2. **Activez l'Audit d'Accès sur CLI**:
   Configurez l'audit pour surveiller l'accès aux fichiers et dossiers sur le client CLI.

   ```powershell
   # Activer l'audit d'accès aux fichiers et dossiers
   Auditpol.exe /set /subcategory:"File System" /success:enable /failure:enable
   ```

3. **Testez la Présence des Événements sur le Journal du Collecteur SRV**:
   Après avoir configuré l'audit, effectuez des actions qui déclencheront ces audits (comme la création d'un utilisateur sur DC et l'accès à un fichier sur CLI), puis vérifiez que les événements correspondants sont bien collectés et enregistrés sur SRV.

   ```powershell
   # Vérifier les événements collectés sur SRV
   Get-WinEvent -LogName "ForwardedEvents" | Where-Object { $_.Message -match "User Account Management" }
   ```

### Conseils d'Utilisation et Bonnes Pratiques

- **Tester Complètement**: Après la configuration initiale, effectuez des tests complets pour vous assurer que tous les événements sont correctement audités et collectés.
- **Sécurité des Données**: Assurez-vous que seuls les utilisateurs autorisés ont accès aux journaux d'événements pour éviter les fuites d'informations sensibles.
- **Maintenance Régulière**: Surveillez et maintenez régulièrement les systèmes d'audit pour éviter les surcharges et assurer une collecte efficace des journaux.

Ces étapes permettent d'établir une base solide pour la gestion des journaux d'événements dans un environnement contrôlé, offrant des insights précieux sur les activités système pour la sécurité et le dépannage.


