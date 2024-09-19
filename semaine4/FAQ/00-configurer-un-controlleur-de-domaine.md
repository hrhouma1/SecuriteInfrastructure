# Guide détaillé pour la création d'un domaine Active Directory et l'ajout d'un client Windows 10

Ce guide vous aidera à configurer un contrôleur de domaine Active Directory et à y joindre un client Windows 10 en utilisant PowerShell. Il est conçu pour les débutants et fournit des explications détaillées à chaque étape.

## Table des matières

1. [Prérequis](#prérequis)
2. [Configuration du serveur](#configuration-du-serveur)
3. [Configuration du client Windows 10](#configuration-du-client-windows-10)
4. [Dépannage](#dépannage)
5. [Conclusion](#conclusion)

## Prérequis

- Un serveur Windows Server (2016 ou ultérieur)
- Un ordinateur client Windows 10
- Les deux machines doivent être sur le même réseau
- Droits d'administrateur sur les deux machines

## Configuration du serveur

### Étape 1 : Installation du rôle AD DS

1. Ouvrez PowerShell en tant qu'administrateur sur le serveur.
2. Exécutez la commande suivante pour installer le rôle Active Directory Domain Services :

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

Cette commande installe les composants nécessaires pour créer un contrôleur de domaine.

### Étape 2 : Promotion du serveur en contrôleur de domaine

1. Toujours dans PowerShell, exécutez le script suivant :

```powershell
$domainName = "votredomaine.local"
$netbiosName = "VOTREDOMAINE"
$safeModeAdminPassword = ConvertTo-SecureString "MotDePasseComplexe" -AsPlainText -Force

Install-ADDSForest `
    -DomainName $domainName `
    -DomainNetbiosName $netbiosName `
    -InstallDns `
    -ForestMode "WinThreshold" `
    -DomainMode "WinThreshold" `
    -SafeModeAdministratorPassword $safeModeAdminPassword `
    -Force
```

**Explication des paramètres :**
- `$domainName` : Nom de domaine complet (FQDN) de votre nouveau domaine.
- `$netbiosName` : Nom NetBIOS du domaine (limité à 15 caractères).
- `$safeModeAdminPassword` : Mot de passe pour le mode de récupération des services d'annuaire (DSRM).
- `-InstallDns` : Installe et configure le rôle DNS sur le serveur.
- `-ForestMode` et `-DomainMode` : Définissent le niveau fonctionnel de la forêt et du domaine.

2. Le serveur redémarrera automatiquement après l'installation.

### Étape 3 : Vérification de la configuration

Après le redémarrage, connectez-vous avec les identifiants d'administrateur du domaine et exécutez :

```powershell
Get-ADDomain
```

Cette commande affichera les informations sur le domaine nouvellement créé.

### Étape 4 : Configuration du pare-feu

Exécutez les commandes suivantes pour autoriser le trafic nécessaire :

```powershell
Set-NetFirewallRule -DisplayGroup "Windows Remote Management" -Enabled True
Set-NetFirewallRule -DisplayGroup "File and Printer Sharing" -Enabled True
```

Ces commandes autorisent la gestion à distance et le partage de fichiers, essentiels pour le fonctionnement du domaine.

## Configuration du client Windows 10

### Étape 1 : Configuration du DNS

1. Ouvrez PowerShell en tant qu'administrateur sur le client Windows 10.
2. Exécutez les commandes suivantes :

```powershell
$interfaceIndex = (Get-NetAdapter).InterfaceIndex
Set-DnsClientServerAddress -InterfaceIndex $interfaceIndex -ServerAddresses "AdresseIPDuDC"
```

Remplacez "AdresseIPDuDC" par l'adresse IP réelle de votre contrôleur de domaine.

### Étape 2 : Vérification de la résolution DNS

Testez la résolution DNS en exécutant :

```powershell
nslookup votredomaine.local
```

Assurez-vous que le nom de domaine est correctement résolu.

### Étape 3 : Jonction au domaine

Exécutez le script suivant pour joindre le client au domaine :

```powershell
$domainName = "votredomaine.local"
$credential = Get-Credential -Message "Entrez les identifiants d'un administrateur de domaine"

Add-Computer -DomainName $domainName -Credential $credential -Restart -Force
```

Entrez les identifiants d'un administrateur de domaine lorsque vous y êtes invité.

## Dépannage

Si vous rencontrez des problèmes, voici quelques étapes de dépannage :

### Sur le serveur

1. Vérifiez que le service Serveur est démarré :

```powershell
Start-Service LanmanServer
Set-Service LanmanServer -StartupType Automatic
```

2. Assurez-vous que le partage de fichiers est activé :

```powershell
Get-NetFirewallRule -DisplayGroup "File and Printer Sharing"
```

### Sur le client

1. Testez la connectivité avec le serveur :

```powershell
ping votredomaine.local
ping AdresseIPDuDC
```

2. Activez la découverte réseau :

```powershell
Get-NetConnectionProfile | Set-NetConnectionProfile -NetworkCategory Private
```

3. Redémarrez le service Explorateur de réseau :

```powershell
Restart-Service -Name "FDResPub"
```

## Conclusion

- Ce guide vous a montré comment configurer un contrôleur de domaine Active Directory et y joindre un client Windows 10 en utilisant PowerShell. 
- N'oubliez pas de personnaliser les noms de domaine, les mots de passe et les adresses IP selon vos besoins spécifiques.
- Si vous rencontrez des difficultés, consultez les journaux d'événements sur les deux machines pour obtenir plus d'informations sur les erreurs potentielles.
