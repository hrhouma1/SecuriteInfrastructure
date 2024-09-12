# **1. Configuration d'Active Directory et Création du Domaine "cafe.local"**

# **Étape 0 : Configuration Réseau et Attribution d'Adresses IP**

Avant de procéder à l'installation d'Active Directory, il est essentiel de configurer correctement le réseau et de s'assurer que le serveur dispose d'une adresse IP statique et d'un DNS fonctionnel.

# **Étape 0.1 : Attribution d'une IP Statique au Serveur**

1. **Attribuer une adresse IP statique :**

   Ouvrez PowerShell en tant qu'administrateur et configurez une adresse IP statique pour le serveur. Remplacez les valeurs des paramètres en fonction de votre réseau.

   ```powershell
   # Attribuer une adresse IP statique
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "192.168.1.10" -PrefixLength 24 -DefaultGateway "192.168.1.1"

   # Configurer le DNS pour pointer vers le serveur lui-même
   Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"
   ```

2. **Vérifier la configuration réseau :**

   Vérifiez que l'adresse IP et le DNS ont bien été configurés.

   ```powershell
   Get-NetIPAddress
   Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
   ```

# **Étape 0.2 : Vérification de la Connectivité Réseau**

1. **Tester la connectivité réseau avec d'autres machines :**

   Depuis le serveur, vérifiez que vous pouvez communiquer avec d'autres machines du réseau.

   ```powershell
   ping 192.168.1.1 # Tester la passerelle par défaut
   ```

---

# **Étape 1 : Installation du rôle Active Directory Domain Services (AD DS)**

Commencez par installer le rôle AD DS sur votre serveur Windows Server 2016 ou ultérieur.

```powershell
# Installer le rôle AD DS
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

---

# **Étape 2 : Promotion du Serveur en tant que Contrôleur de Domaine pour "cafe.local"**

Promouvez votre serveur en tant que contrôleur de domaine en créant une nouvelle forêt **cafe.local**.

```powershell
# Promouvoir le serveur en tant que contrôleur de domaine
Install-ADDSForest -DomainName "cafe.local" -DomainNetBiosName "CAFE" -InstallDNS
```

Le serveur redémarrera automatiquement pour finaliser la promotion.

---

# **Étape 3 : Vérification de l'Installation**

Après le redémarrage du serveur, vérifiez que le domaine et le serveur DNS ont été correctement configurés.

```powershell
# Vérifier que le serveur est devenu un contrôleur de domaine
Get-ADDomain

# Vérifier la configuration DNS
Get-DnsServerZone
```

---

## **2. Désactivation de SMB 1.0 et Activation du Chiffrement SMB 3.1.1**

# **Étape 4 : Désactivation de SMB 1.0**

Pour renforcer la sécurité de votre serveur, désactivez SMB 1.0, un protocole obsolète.

```powershell
# Désactiver SMB 1.0
Set-SmbServerConfiguration –EnableSMB1Protocol $false
```

Si vous souhaitez désinstaller SMB 1.0 :

```powershell
# Désinstaller SMB 1.0
Remove-WindowsFeature FS-SMB1
```

# **Étape 5 : Activer l'Audit de SMB 1.0 (facultatif)**

Pour surveiller toute tentative d'utilisation de SMB 1.0, activez l'audit.

```powershell
# Activer l’audit SMB 1.0
Set-SmbServerConfiguration –AuditSmb1Access $true

# Visualiser les événements d’audit
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
```

---

## **3. Création et Chiffrement d'un Dossier Partagé avec SMB 3.1.1**

# **Étape 6 : Création d'un Dossier Partagé avec Chiffrement SMB**

Créez un dossier sur votre serveur et configurez un partage avec chiffrement activé.

```powershell
# Créer un dossier
New-Item -ItemType Directory -Path "C:\SecureFolder"

# Créer un partage SMB avec chiffrement activé
New-SmbShare -Name "SecureShare" -Path "C:\SecureFolder" -EncryptData $true

# Accorder des permissions de contrôle total à "Everyone"
Grant-SmbShareAccess -Name "SecureShare" -AccountName "Everyone" -AccessRight Full -Force

# Configurer les permissions NTFS pour le dossier
$acl = Get-Acl "C:\SecureFolder"
$permission = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($permission)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl
```

# **Étape 7 : Configurer le Chiffrement SMB sur Tous les Partages**

Si vous avez d'autres partages sur le serveur, vous pouvez activer le chiffrement SMB globalement.

```powershell
# Activer le chiffrement SMB sur tous les partages
Set-SmbServerConfiguration –EncryptData $true
```

---

## **4. Configuration de l'Audit des Accès sur le Partage SMB**

# **Étape 8 : Activer l'Audit des Accès sur le Dossier Partagé**

Configurez l'audit des accès pour surveiller les activités sur le partage.

```powershell
# Activer l'audit des accès aux objets
auditpol /list /category
auditpol /set /category:"Object Access" /subcategory:"File System" /success:enable /failure:enable
# Cette commande permet d'auditer les accès aux objets du système de fichiers avec un rapport sur les succès et les échecs. Vous pouvez la tester pour vous assurer que tout fonctionne correctement.


# Activer l'audit sur le dossier partagé
$AuditRule = New-Object System.Security.AccessControl.FileSystemAuditRule("Everyone", "Read, Write", "ContainerInherit,ObjectInherit", "None", "Success, Failure")
$acl = Get-Acl "C:\SecureFolder"
$acl.AddAuditRule($AuditRule)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl
```

# **Étape 9 : Récupérer les Événements d'Audit et les Exporter en CSV**

Collectez et exportez les événements d'audit pour analyse.

```powershell
# Récupérer les événements d’audit
$events = Get-WinEvent -LogName Security | Where-Object { $_.Id -eq 4663 -and $_.Properties[5].Value -like "*C:\SecureFolder*" }

# Exporter les événements en CSV
$events | Select-Object TimeCreated, @{Name="User"; Expression={$_.Properties[1].Value}}, @{Name="File Accessed"; Expression={$_.Properties[5].Value}}, @{Name="Access Mask"; Expression={$_.Properties[8].Value}} | Export-Csv -Path "C:\SecureFolderAudit.csv" -NoTypeInformation
```

---

## **5. Connexion d'un Autre Ordinateur au Domaine "cafe.local"**

# **Étape 10 : Configurer le Client pour Rejoindre le Domaine**

Sur l'ordinateur client (Windows 10), configurez le DNS pour pointer vers le serveur Active Directory.

```powershell
# Configurer l'adresse DNS du client
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"
```

# **Étape 11 : Joindre l'Ordinateur Client au Domaine**

Ajoutez le client au domaine **cafe.local**.

```powershell
# Joindre l'ordinateur au domaine "cafe.local"
Add-Computer -DomainName "cafe.local" -Credential cafe\Administrator -Restart
```

# **Étape 12 : Vérifier la Connexion au Domaine**

Après le redémarrage, vérifiez que l'ordinateur a rejoint le domaine.

```powershell
# Vérifier l'appartenance au domaine
(Get-WmiObject Win32_ComputerSystem).Domain
```

---

## **6. Vérification de la Version de SMB Utilisée**

# **Étape 13 : Vérification de la Version SMB sur le Serveur**

Vérifiez les versions SMB activées sur le serveur.

```powershell
Get-SmbServerConfiguration | Select-Object EnableSMB1Protocol, EnableSMB2Protocol
```

# **Étape 14 : Vérification de la Version SMB Utilisée par une Connexion**

Vérifiez la version de SMB utilisée par une connexion active.

```powershell
Get-SmbConnection
```

---

# Annexe :  les corrections et améliorations 

### Problème avec la règle d'accès ACL et l'utilisation de la commande suivante : 


```powershell
# Problème : utilisation de "Everyone" dans un système non anglophone
New-SmbShare -Name "Chapitre2" -Path "C:\SRV" -FullAccess "Everyone"
```

- Cette commande posait problème car le groupe "Everyone" n'existe pas dans les systèmes où l'interface est localisée dans une autre langue, comme le français, où il faut utiliser "Tout le monde". 
- La correction consistait à remplacer "Everyone" par une version localisée ou un groupe plus spécifique, comme :

```powershell
# Correction : utilisation de "Tout le monde" ou d'un groupe plus spécifique
New-SmbShare -Name "Chapitre2" -Path "C:\SRV" -FullAccess "Tout le monde"
```

Ou bien utiliser des groupes spécifiques tels que `"NT AUTHORITY\Authenticated Users"` ou `"BUILTIN\Administrators"`.



Pour résumer, le problème  rencontrez est lié à l'utilisation de "Everyone" dans un système non anglophone. Il est recommandé d'utiliser soit :
- La version localisée comme `"Tout le monde"` pour un système français, ou
- Des groupes plus spécifiques comme `"NT AUTHORITY\Authenticated Users"` ou `"BUILTIN\Administrators"`, qui fonctionnent dans la plupart des environnements.

### Correction du Script Complet

## script complet corrigé :

```powershell
# Attente de 60 secondes
Start-Sleep -Seconds 60

# Obtenir des informations sur le domaine
Get-ADDomain

# Obtenir la zone DNS
Get-DnsServerZone

# Désactiver le protocole SMB 1.0
Set-SmbServerConfiguration –EnableSMB1Protocol $false

# Supprimer la fonctionnalité SMB 1.0 si installée
Remove-WindowsFeature FS-SMB1

# Activer l'audit de l'accès à SMB 1.0
Set-SmbServerConfiguration –AuditSmb1Access $true

# Visualiser les événements d'audit SMB
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit

# Créer un nouveau dossier dans C:\SRV
New-Item -ItemType Directory -Path "C:\SRV"

# Créer un partage SMB avec chiffrement activé
New-SmbShare -Name "Chapitre2" -Path "C:\SRV" -EncryptData $true

# Révoquer les accès pour "Everyone" (ou "Tout le monde")
Revoke-SmbShareAccess -Name "Chapitre2" -AccountName "Everyone" -Force

# Récupérer les ACL du dossier
$acl = Get-Acl "C:\SRV"

# Créer une règle d'accès avec un compte universel comme "NT AUTHORITY\Authenticated Users"
$permission = New-Object System.Security.AccessControl.FileSystemAccessRule("NT AUTHORITY\Authenticated Users", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")

# Appliquer la règle d'accès au dossier
$acl.SetAccessRule($permission)
Set-Acl -Path "C:\SRV" -AclObject $acl

# Vérifier les partages SMB
Get-SmbShare

# Vérifier les accès au partage
Get-SmbShareAccess -Name "Chapitre2"
```

### Explications :

1. **Identités universelles** : J'ai remplacé `"Everyone"` par `"NT AUTHORITY\Authenticated Users"` car cela fonctionne de manière plus universelle, quel que soit la langue du système d'exploitation.
2. **Suppression des accès de "Everyone"** : La commande `Revoke-SmbShareAccess` révoque les accès précédents pour "Everyone" (ou son équivalent local).
3. **Création de règles ACL** : Nous utilisons une règle d'accès pour un groupe qui devrait être reconnu dans tous les environnements.

### Testez le script en exécutant ces commandes une par une pour vous assurer que tout fonctionne comme prévu. 




# Annexe  2



Il est essentiel de vérifier les catégories d'audit disponibles sur votre système avant de configurer l'audit, car les noms peuvent varier selon la langue du système d'exploitation (anglais vs. français).

1. **Vérification des catégories d'audit disponibles** :
   Utilisez la commande suivante pour lister toutes les catégories d'audit disponibles sur le système. Cela permet de vérifier si les noms de catégories et sous-catégories comme "Object Access" ou "File System" sont traduits dans votre langue.

   ```powershell
   auditpol /list /category
   ```

2. **Vérification du groupe "Everyone"** :
   Dans certains systèmes non anglophones, le groupe `"Everyone"` peut être localisé sous le nom `"Tout le monde"`. Pour éviter les erreurs lors de l'application des permissions, il est recommandé de vérifier si `"Everyone"` est traduit sur votre système avec la commande suivante :

   ```powershell
   Get-LocalGroup
   ```

   Cette commande listera tous les groupes locaux présents sur le système, y compris leur nom exact dans la langue du système d'exploitation.

En effectuant ces vérifications, vous éviterez des erreurs liées à la localisation et vous assurerez que les bonnes catégories d'audit et groupes sont utilisés dans les configurations.


----
# Annexe 3 : 
----

### Il faut faire attention aux commandes PowerShell où des conflits potentiels peuvent survenir en raison des différences linguistiques entre les systèmes en anglais et en français :

1. **Nom du groupe "Everyone"**
   - Dans un environnement en anglais, "Everyone" est utilisé pour accorder l'accès à tous les utilisateurs. Cependant, dans un système en français, ce groupe est souvent traduit par "Tout le monde". Il est donc préférable d'utiliser des identités universelles comme `"NT AUTHORITY\Authenticated Users"`.

   Commandes affectées :
   ```powershell
   Grant-SmbShareAccess -Name "SecureShare" -AccountName "Everyone" -AccessRight Full -Force
   $permission = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
   $AuditRule = New-Object System.Security.AccessControl.FileSystemAuditRule("Everyone", "Read, Write", "ContainerInherit,ObjectInherit", "None", "Success, Failure")
   Revoke-SmbShareAccess -Name "Chapitre2" -AccountName "Everyone" -Force
   ```

2. **Commandes de configuration du partage SMB**
   - Utilisation de noms de groupes dans les commandes `Grant-SmbShareAccess` et `Revoke-SmbShareAccess`, qui peuvent causer des problèmes dans un système en français si les noms de groupes ne sont pas adaptés (e.g., "Everyone" vs "Tout le monde").

3. **Audit d'accès aux objets**
   - La commande `auditpol` pour activer l'audit des accès aux objets utilise des noms de catégories et sous-catégories qui doivent correspondre à la langue du système d'exploitation. Par exemple, "Object Access" pourrait être traduit en "Accès aux objets" dans un système en français.

   Commande affectée :
   ```powershell
   auditpol /set /category:"Object Access" /subcategory:"File System" /success:enable /failure:enable
   ```

### Recommandations :
- **Utiliser des identités universelles** : Dans les commandes qui utilisent "Everyone", il est préférable de remplacer ce groupe par `"NT AUTHORITY\Authenticated Users"` ou `"BUILTIN\Administrators"`, qui sont reconnus indépendamment de la langue du système.
- **Vérifier les noms de catégories et sous-catégories dans `auditpol`** : Pour un système en français, il est conseillé de vérifier les traductions des catégories et sous-catégories utilisées pour l'audit.

Cela devrait prévenir les conflits liés à la localisation du système d'exploitation.
