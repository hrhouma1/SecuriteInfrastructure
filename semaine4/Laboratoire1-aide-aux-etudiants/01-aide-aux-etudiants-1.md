---------------------------------------------------------------------------------------
# Étape 01 : Adressage et Configuration des Rôles
---------------------------------------------------------------------------------------

# 1) Calcul du Subnetting

#### Réseau (A) : Jusqu'à 10 adresses IP
- **Masque de sous-réseau**: `/28` (255.255.255.240)
- **NetID**: 10.11.12.0
- **Première adresse utilisable**: 10.11.12.1
- **Dernière adresse utilisable**: 10.11.12.14

#### Réseau (B) : Jusqu'à 12 adresses IP
- **Masque de sous-réseau**: `/28` (255.255.255.240)
- **NetID**: 10.11.12.16
- **Première adresse utilisable**: 10.11.12.17
- **Dernière adresse utilisable**: 10.11.12.30

### Tableau d’Adressage des Sous-Réseaux

| Réseau | NetID       | Première Adresse Utilisable | Dernière Adresse Utilisable | Masque de Sous-Réseau | Préfixe CIDR |
|--------|-------------|-----------------------------|-----------------------------|-----------------------|--------------|
| A      | 10.11.12.0  | 10.11.12.1                  | 10.11.12.14                 | 255.255.255.240       | /28          |
| B      | 10.11.12.16 | 10.11.12.17                 | 10.11.12.30                 | 255.255.255.240       | /28          |

# 2) Configuration des Rôles des Machines avec PowerShell

#### Serveur SRV
1. **Configurer l'adresse IP**
   ```powershell
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.11.12.x -PrefixLength 28 -DefaultGateway 10.11.12.y
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses ("DNS_IP")
   ```

2. **Installer le rôle de serveur de fichiers**
   ```powershell
   Install-WindowsFeature FS-FileServer
   ```

3. **Créer un partage SMB**
   ```powershell
   New-SmbShare -Name "Labo1" -Path "C:\Labo1" -FullAccess "Everyone"
   ```

#### Serveur Router
1. **Configurer les adresses IP pour chaque interface**
   ```powershell
   # Interface vers le réseau A
   New-NetIPAddress -InterfaceAlias "Ethernet1" -IPAddress 10.11.12.x -PrefixLength 28

   # Interface vers le réseau B
   New-NetIPAddress -InterfaceAlias "Ethernet2" -IPAddress 10.11.12.y -PrefixLength 28

   # Interface vers Internet (exemple)
   New-NetIPAddress -InterfaceAlias "Ethernet3" -IPAddress <Internet_IP> -PrefixLength <Prefix>
   ```

2. **Activer le routage**
   ```powershell
   Install-WindowsFeature RemoteAccess, RSAT-RemoteAccess-PowerShell
   ```

#### Serveur DC
1. **Configurer l'adresse IP**
   ```powershell
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.11.12.z -PrefixLength 28 -DefaultGateway 10.11.12.x
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses ("DNS_IP")
   ```

2. **Installer Active Directory Domain Services**
   ```powershell
   Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
   Import-Module ADDSDeployment
   Install-ADDSForest -DomainName "Contoso.net" -InstallDNS
   ```

#### Client CLI
1. **Configurer l'adresse IP**
   ```powershell
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.11.x.y -PrefixLength 28 -DefaultGateway 10.x.y.z
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses ("DNS_IP")
   ```

2. **Joindre le domaine Contoso.net**
   ```powershell
   Add-Computer -DomainName "Contoso.net" -Credential Contoso\AdminUser
   Restart-Computer
   ```

Ces étapes vous permettront de configurer automatiquement votre environnement de laboratoire en utilisant PowerShell, assurant ainsi une mise en place rapide et cohérente de votre infrastructure réseau et serveur.



---------------------------------------------------------------------------------------
# Étape 02 - Analyse du Trafic Lié au Partage SMB
---------------------------------------------------------------------------------------


# 1) Installation de Wireshark sur SRV

Microsoft Message Analyzer n'étant plus disponible, utilisez Wireshark.

#### Installation de Wireshark

```powershell
choco install wireshark
```

# 2) Création d'un Dossier Partagé sur SRV

```powershell
New-Item -Path "C:\Labo1" -ItemType Directory
New-SmbShare -Name "Labo1" -Path "C:\Labo1" -FullAccess "Everyone"
```

# 3) Capture du Trafic Réseau Non Chiffré

#### a) Création et Copie d'un Fichier sur CLI

```cmd
echo "This is a secret file." > MonSecret.txt
copy MonSecret.txt \\SRV\Labo1\
copy C:\Windows\System32\WindowsCodecsRaw.txt \\SRV\Labo1\
```

#### b) Capture avec Wireshark sur SRV

- Démarrez Wireshark et commencez une capture sur l'interface réseau appropriée.
- Filtrez le trafic pour afficher uniquement les paquets SMB/SMB2 impliquant CLI.

#### c) Analyse du Trafic

- Appliquez un filtre pour afficher uniquement le trafic SMB/SMB2.
- Utilisez les fonctionnalités de couleur pour mettre en évidence les paquets contenant la chaîne "TXT".

# 4) Activation d'IPsec sur DC

#### Configuration d'IPsec

```powershell
New-NetIPsecRule -DisplayName "SMB Encryption" -Direction Inbound -Action RequireInbound -Protocol TCP -LocalPort 445 -RemotePort 445 -InboundSecurity Require
gpupdate /force
```

# 5) Capture du Trafic Réseau Chiffré

- Démarrez une nouvelle session de capture sur SRV.
- Copiez le fichier depuis CLI vers SRV et observez le trafic chiffré (ESP).

# 6) Désactivation d'IPsec sur DC

```powershell
Remove-NetIPsecRule -DisplayName "SMB Encryption"
gpupdate /force
```

### Test du Chiffrement SMB 3.1.1

# 10) Vérification de la Désactivation d'IPsec

- Assurez-vous qu'aucun trafic ESP n'est capturé.

# 11) Création d'un Partage Chiffré avec SMB sur SRV

```powershell
Set-SmbServerConfiguration -EncryptData $true
New-SmbShare -Name "Labo1" -Path "C:\Labo1" -FullAccess "Everyone"
```

# 12) Définition des Permissions

```powershell
Grant-SmbShareAccess -Name "Labo1" -AccountName "Everyone" -AccessRight Full
```

# 13) Vérification du Chiffrement SMB

- Lancez une capture dans Wireshark et copiez à nouveau les fichiers pour vérifier que le trafic SMB est chiffré.

Ces étapes vous permettront de configurer et d'analyser efficacement le trafic SMB dans votre environnement de laboratoire.














---------------------------------------------------------------------------------------
# Troisième Partie : Transfert des Journaux d’Événements
---------------------------------------------------------------------------------------

# 1) Configurer DC et CLI comme Sources d’Événements

- **Connectez-vous à DC et CLI** en tant qu'administrateur de domaine.
- Ouvrez l'**Observateur d'événements** (`eventvwr.msc`) pour vérifier que les journaux sont générés correctement.

# 2) Activer WinRM via un GPO

- Sur **DC**, ouvrez la **Gestion des stratégies de groupe** (`gpmc.msc`).
- Créez une **nouvelle GPO** pour activer WinRM, puis éditez-la :
  - Allez à **Configuration de l’ordinateur > Stratégies > Modèles d'administration > Composants Windows > Windows Remote Management (WinRM) > Service WinRM**.
  - Activez **Autoriser la gestion à distance du serveur via WinRM**.
- Appliquez la GPO à l'unité d'organisation contenant DC et CLI.
- Forcez la mise à jour des stratégies avec `gpupdate /force`.

# 3) Ajouter des Utilisateurs au Groupe Administrateurs Locaux via GPO

- Modifiez la même GPO :
  - Allez à **Configuration de l'ordinateur > Préférences > Paramètres du Panneau de configuration > Utilisateurs et groupes locaux**.
  - Créez une nouvelle entrée pour ajouter un utilisateur au groupe **Administrateurs**.

# 4) Configurer SRV comme Collecteur d’Événements

- Sur **SRV**, ouvrez l'**Observateur d'événements**.
- Cliquez droit sur **Abonnements**, puis sélectionnez **Créer un abonnement**.
- Configurez l'abonnement :
  - **Nom** : LogLabo1
  - **Destination Log** : Événements transférés
  - **Ordinateurs sources des événements** : Ajoutez DC et CLI.

# 5) Créer et Configurer un Abonnement d’Événement "LogLabo1"

- Dans les propriétés de l'abonnement, sélectionnez les types de journaux à collecter (ex. Système, Application).

# 6) Activer l’Audit de Création des Utilisateurs sur DC

- Ouvrez la **Gestion des stratégies de groupe**, modifiez une GPO :
  - Allez à **Configuration de l'ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies avancées d'audit > Gestion des comptes**.
  - Activez **Audit User Account Management**.
- Créez un utilisateur nommé Bob dans Active Directory.

# 7) Activer l’Audit d’Accès sur CLI

- Créez un répertoire `c:\rep1`.
- Faites un clic droit sur le dossier, allez dans **Propriétés > Sécurité > Avancé > Auditer**, ajoutez un principal (ex. Tout le monde), et activez l’audit pour les accès réussis et échoués.
- Créez un fichier `fich.txt` dans `c:\rep1`.

# 8) Vérifier la Présence des Événements sur SRV

- Sur SRV, ouvrez l'Observateur d'événements et allez dans **Événements transférés**.
- Vérifiez que les événements suivants sont présents :
  - Création de l'utilisateur Bob sur DC.
  - Accès au répertoire `c:\rep1` et création du fichier `fich.txt` sur CLI.

Ces étapes vous permettront de configurer efficacement le transfert des journaux d'événements dans votre environnement. Assurez-vous que toutes les configurations sont correctement appliquées pour garantir la collecte et la surveillance centralisées.



