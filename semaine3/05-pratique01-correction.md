# **1. Configuration d'Active Directory et Création du Domaine "cafe.local"**

# **Étape 1 : Installation du rôle Active Directory Domain Services (AD DS)**

Commencez par installer le rôle AD DS sur votre serveur Windows Server 2016 ou ultérieur.

```powershell
# Installer le rôle AD DS
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

# **Étape 2 : Promotion du serveur en tant que contrôleur de domaine pour "cafe.local"**

Promouvez votre serveur en tant que contrôleur de domaine en créant une nouvelle forêt **cafe.local**.

```powershell
# Promouvoir le serveur en tant que contrôleur de domaine
Install-ADDSForest -DomainName "cafe.local" -DomainNetBiosName "CAFE" -InstallDNS
```

Après cette étape, le serveur redémarrera automatiquement pour finaliser la promotion.

# **Étape 3 : Vérification de l'installation**

Une fois le serveur redémarré, vous pouvez vérifier que le domaine et le serveur DNS ont été correctement configurés.

```powershell
# Vérifier que le serveur est devenu un contrôleur de domaine
Get-ADDomain

# Vérifier la configuration du DNS
Get-DnsServerZone
```

---

## **2. Désactivation de SMB 1.0 et Activation du chiffrement SMB 3.1.1**

# **Étape 4 : Désactivation de SMB 1.0**

Pour renforcer la sécurité de votre serveur, désactivez SMB 1.0, un protocole obsolète.

```powershell
# Désactiver SMB 1.0
Set-SmbServerConfiguration –EnableSMB1Protocol $false
```

Si vous souhaitez également désinstaller SMB 1.0, utilisez la commande suivante :

```powershell
# Désinstaller SMB 1.0
Remove-WindowsFeature FS-SMB1
```

# **Étape 5 : Activer l'audit de SMB 1.0 (facultatif)**

Pour surveiller toute tentative d'utilisation de SMB 1.0, activez l'audit de SMB 1.0.

```powershell
# Activer l’audit SMB 1.0
Set-SmbServerConfiguration –AuditSmb1Access $true

# Visualiser les événements d’audit SMB 1.0
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
```

---

## **3. Création et Chiffrement d'un Dossier Partagé avec SMB 3.1.1**

# **Étape 6 : Création d'un dossier partagé et chiffrement SMB**

Créez un nouveau dossier sur votre serveur, puis configurez un partage avec chiffrement SMB 3.1.1 activé.

```powershell
# Créer un dossier sur le serveur
New-Item -ItemType Directory -Path "C:\SecureFolder"

# Créer un partage SMB avec chiffrement activé
New-SmbShare -Name "SecureShare" -Path "C:\SecureFolder" -EncryptData $true

# Accorder des permissions de contrôle total à "Everyone" pour l'accès au partage SMB
Grant-SmbShareAccess -Name "SecureShare" -AccountName "Everyone" -AccessRight Full -Force

# Configurer les permissions NTFS pour le dossier
$acl = Get-Acl "C:\SecureFolder"
$permission = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($permission)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl
```

# **Étape 7 : Configurer le chiffrement SMB sur tous les partages existants**

Si vous avez d'autres partages sur le serveur, vous pouvez activer le chiffrement SMB pour tous les partages à la fois.

```powershell
# Activer le chiffrement SMB sur tous les partages du serveur
Set-SmbServerConfiguration –EncryptData $true
```

---

## **4. Configuration de l'Audit des Accès sur le Partage SMB**

# **Étape 8 : Activer l'audit des accès sur le dossier partagé**

Pour surveiller les accès au dossier partagé, activez l'audit des accès aux objets.

```powershell
# Activer l'audit des accès aux objets
auditpol /set /category:"Object Access" /success:enable /failure:enable

# Activer l'audit sur le dossier partagé
$AuditRule = New-Object System.Security.AccessControl.FileSystemAuditRule("Everyone", "Read, Write", "ContainerInherit,ObjectInherit", "None", "Success, Failure")
$acl = Get-Acl "C:\SecureFolder"
$acl.AddAuditRule($AuditRule)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl
```

# **Étape 9 : Récupérer les événements d'audit et les exporter en CSV**

Collectez les événements d'audit relatifs aux accès sur le dossier partagé et exportez-les dans un fichier CSV pour analyse.

```powershell
# Récupérer les événements d’audit relatifs au dossier partagé
$events = Get-WinEvent -LogName Security | Where-Object { $_.Id -eq 4663 -and $_.Properties[5].Value -like "*C:\SecureFolder*" }

# Exporter les événements dans un fichier CSV
$events | Select-Object TimeCreated, @{Name="User"; Expression={$_.Properties[1].Value}}, @{Name="File Accessed"; Expression={$_.Properties[5].Value}}, @{Name="Access Mask"; Expression={$_.Properties[8].Value}} | Export-Csv -Path "C:\SecureFolderAudit.csv" -NoTypeInformation
```

---

## **5. Connexion d'un autre ordinateur au Domaine "cafe.local"**

# **Étape 10 : Configurer le client pour rejoindre le domaine**

Sur l'ordinateur client (Windows 10, par exemple), configurez le DNS pour pointer vers le serveur Active Directory afin de pouvoir joindre le domaine **cafe.local**.

```powershell
# Configurer l'adresse DNS du client pour pointer vers le serveur AD DS
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"
```

# **Étape 11 : Joindre l'ordinateur client au domaine**

Ajoutez maintenant l'ordinateur client au domaine **cafe.local**.

```powershell
# Joindre l'ordinateur au domaine "cafe.local"
Add-Computer -DomainName "cafe.local" -Credential cafe\Administrator -Restart
```

# **Étape 12 : Vérifier la connexion de l'ordinateur au domaine**

Après le redémarrage, vérifiez que l'ordinateur a bien rejoint le domaine.

```powershell
# Vérifier que l'ordinateur fait partie du domaine "cafe.local"
(Get-WmiObject Win32_ComputerSystem).Domain
```

---

## **6. Vérification de la Version de SMB Utilisée**

Enfin, vérifiez la version de SMB utilisée sur le serveur ou par un client connecté au serveur.

# **Étape 13 : Vérification de la version SMB sur le serveur**

```powershell
# Vérifier les versions de SMB activées sur le serveur
Get-SmbServerConfiguration | Select-Object EnableSMB1Protocol, EnableSMB2Protocol
```

# **Étape 14 : Vérification de la version SMB utilisée par une connexion**

```powershell
# Vérifier la version SMB utilisée par une connexion SMB active
Get-SmbConnection
```

---

## **Résumé**

En suivant ce tutoriel, vous avez :

1. **Installé et configuré Active Directory**, en promouvant un serveur en tant que contrôleur de domaine pour **cafe.local**.
2. **Désactivé SMB 1.0** et **activé SMB 3.1.1** avec chiffrement sur un partage.
3. **Configuré un dossier partagé sécurisé avec chiffrement SMB** et **mis en place l'audit des accès** pour surveiller les tentatives d'accès au partage.
4. **Connecté un autre ordinateur au domaine** et vérifié l'utilisation du protocole SMB.

Vous pouvez ainsi garantir un environnement sécurisé avec un contrôle des accès via l'audit et la désactivation des versions obsolètes de SMB.

# Annexe 01





```powershell
# Installation d'AD DS
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promotion du serveur en tant que contrôleur de domaine
Install-ADDSForest -DomainName "cafe.local" -DomainNetBiosName "CAFE" -InstallDNS

# Vérification du domaine et du DNS
Get-ADDomain
Get-DnsServerZone

# Désactivation de SMB 1.0
Set-SmbServerConfiguration –EnableSMB1Protocol $false
Remove-WindowsFeature FS-SMB1

# Activer l’audit SMB 1.0
Set-SmbServerConfiguration –AuditSmb1Access $true
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit

# Création du dossier partagé avec chiffrement SMB
New-Item -ItemType Directory -Path "C:\SecureFolder"
New-SmbShare -Name "SecureShare" -Path "C:\SecureFolder" -EncryptData $true
Grant-SmbShareAccess -Name "SecureShare" -AccountName "Everyone" -AccessRight Full -Force
$acl = Get-Acl "C:\SecureFolder"
$permission = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($permission)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl

# Activer le chiffrement SMB sur tous les partages
Set-SmbServerConfiguration –EncryptData $true

# Activer l'audit des accès aux objets
auditpol /set /category:"Object Access" /success:enable /failure:enable
$AuditRule = New-Object System.Security.AccessControl.FileSystemAuditRule("Everyone", "Read, Write", "ContainerInherit,ObjectInherit", "None", "Success, Failure")
$acl = Get-Acl "C:\SecureFolder"
$acl.AddAuditRule($AuditRule)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl

# Récupérer les événements d’audit et exporter vers un fichier CSV
$events = Get-WinEvent -LogName Security | Where-Object { $_.Id -eq 4663 -and $_.Properties[5].Value -like "*C:\SecureFolder*" }
$events | Select-Object TimeCreated, @{Name="User"; Expression={$_.Properties[1].Value}}, @{Name="File Accessed"; Expression={$_.Properties[5].Value}}, @{Name="Access Mask"; Expression={$_.Properties[8].Value}} | Export-Csv -Path "C:\SecureFolderAudit.csv" -NoTypeInformation

# Configuration du client pour rejoindre le domaine
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"
Add-Computer -DomainName "cafe.local" -Credential cafe\Administrator -Restart

# Vérification de la connexion du client au domaine
(Get-WmiObject Win32_ComputerSystem).Domain

# Vérification de la version SMB sur le serveur
Get-SmbServerConfiguration | Select-Object EnableSMB1Protocol, EnableSMB2Protocol

# Vérification de la version SMB utilisée par une connexion
Get-SmbConnection

# Vérification supplémentaire si nécessaire pour les paramètres SMB globaux
Get-SmbServerConfiguration

# Désactiver SMB 2.0 (si requis pour une compatibilité stricte SMB 3.x uniquement)
Set-SmbServerConfiguration –EnableSMB2Protocol $false

# Activer uniquement les connexions chiffrées pour SMB
Set-SmbServerConfiguration -RejectUnencryptedAccess $true

# Liste des partages SMB actifs avec leur état de chiffrement
Get-SmbShare | Select-Object Name, Path, EncryptData

# Visualiser l'état des règles d'audit du partage SMB
Get-Acl -Path "C:\SecureFolder" | Format-List

# Activer le journal d’audit avancé pour les connexions SMB
auditpol /set /subcategory:"Logon" /success:enable /failure:enable

# Filtrer les événements d'audit relatifs aux connexions SMB sur le serveur
$logEvents = Get-WinEvent -LogName "Security" | Where-Object { $_.Id -eq 5140 }
$logEvents | Export-Csv -Path "C:\SMBConnectionAudit.csv" -NoTypeInformation
```


# Annexe 2

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
Install-ADDSForest -DomainName "cafe.local" -DomainNetBiosName "CAFE" -InstallDNS
Get-ADDomain
Get-DnsServerZone
Set-SmbServerConfiguration –EnableSMB1Protocol $false
Remove-WindowsFeature FS-SMB1
Set-SmbServerConfiguration –AuditSmb1Access $true
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
New-Item -ItemType Directory -Path "C:\SecureFolder"
New-SmbShare -Name "SecureShare" -Path "C:\SecureFolder" -EncryptData $true
Grant-SmbShareAccess -Name "SecureShare" -AccountName "Everyone" -AccessRight Full -Force
$acl = Get-Acl "C:\SecureFolder"
$permission = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($permission)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl
Set-SmbServerConfiguration –EncryptData $true
auditpol /set /category:"Object Access" /success:enable /failure:enable
$AuditRule = New-Object System.Security.AccessControl.FileSystemAuditRule("Everyone", "Read, Write", "ContainerInherit,ObjectInherit", "None", "Success, Failure")
$acl = Get-Acl "C:\SecureFolder"
$acl.AddAuditRule($AuditRule)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl
$events = Get-WinEvent -LogName Security | Where-Object { $_.Id -eq 4663 -and $_.Properties[5].Value -like "*C:\SecureFolder*" }
$events | Select-Object TimeCreated, @{Name="User"; Expression={$_.Properties[1].Value}}, @{Name="File Accessed"; Expression={$_.Properties[5].Value}}, @{Name="Access Mask"; Expression={$_.Properties[8].Value}} | Export-Csv -Path "C:\SecureFolderAudit.csv" -NoTypeInformation
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"
Add-Computer -DomainName "cafe.local" -Credential cafe\Administrator -Restart
(Get-WmiObject Win32_ComputerSystem).Domain
Get-SmbServerConfiguration | Select-Object EnableSMB1Protocol, EnableSMB2Protocol
Get-SmbConnection
Get-SmbServerConfiguration
Set-SmbServerConfiguration –EnableSMB2Protocol $false
Set-SmbServerConfiguration -RejectUnencryptedAccess $true
Get-SmbShare | Select-Object Name, Path, EncryptData
Get-Acl -Path "C:\SecureFolder" | Format-List
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
$logEvents = Get-WinEvent -LogName "Security" | Where-Object { $_.Id -eq 5140 }
$logEvents | Export-Csv -Path "C:\SMBConnectionAudit.csv" -NoTypeInformation
```
