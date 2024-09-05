# ** MÉTHODE 2: Automatisation de la configuration d'Active Directory, du domaine et du chiffrement SMB**

## **Objectif du projet**
Ce projet a pour but de configurer automatiquement un environnement de domaine Active Directory sur Windows Server, d'ajouter des partages sécurisés avec chiffrement SMB 3.1.1, et de permettre à des machines clientes Windows 10 de rejoindre le domaine. Le script PowerShell vous permet de déployer cette configuration de bout en bout sans intervention manuelle majeure.

---

## **Prérequis**

1. **Serveur Windows Server 2016/2019** pour l'installation et la configuration d'Active Directory.
2. **Machine Windows 10** pour l'ajout au domaine.
3. **Droits administratifs** sur les deux machines pour exécuter les scripts PowerShell.
4. **PowerShell** activé sur les deux machines.
5. **Réseau fonctionnel** entre le serveur et la machine cliente pour que la machine Windows 10 puisse communiquer avec le contrôleur de domaine.

---

## **Étapes**

### **1. Script d'installation d'Active Directory et configuration SMB sur le serveur**

Sur le serveur Windows Server 2016 ou 2019, vous devez exécuter le script PowerShell **`setupADSMB.ps1`** pour automatiser l'installation d'Active Directory, la création du domaine **cafe.local**, et la configuration du chiffrement SMB ainsi que de l'audit.

#### **Contenu du script :**

```powershell
# Installation d'Active Directory Domain Services
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promotion du serveur en tant que contrôleur de domaine
Install-ADDSForest -DomainName "cafe.local" -DomainNetBiosName "CAFE" -InstallDNS

# Attendre que le serveur redémarre pour appliquer les changements
Start-Sleep -Seconds 60
Get-ADDomain
Get-DnsServerZone

# Désactivation de SMB 1.0 et désinstallation (si nécessaire)
Set-SmbServerConfiguration –EnableSMB1Protocol $false
Remove-WindowsFeature FS-SMB1

# Activer l’audit SMB 1.0
Set-SmbServerConfiguration –AuditSmb1Access $true
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit

# Création du dossier partagé avec chiffrement SMB
New-Item -ItemType Directory -Path "C:\SecureFolder"
New-SmbShare -Name "SecureShare" -Path "C:\SecureFolder" -EncryptData $true
Grant-SmbShareAccess -Name "SecureShare" -AccountName "Everyone" -AccessRight Full -Force

# Configurer les permissions NTFS
$acl = Get-Acl "C:\SecureFolder"
$permission = New-Object System.Security.AccessControl.FileSystemAccessRule("Everyone", "FullControl", "ContainerInherit,ObjectInherit", "None", "Allow")
$acl.SetAccessRule($permission)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl

# Activer le chiffrement SMB pour tous les partages
Set-SmbServerConfiguration –EncryptData $true

# Activer l'audit des accès aux objets
auditpol /set /category:"Object Access" /success:enable /failure:enable
$AuditRule = New-Object System.Security.AccessControl.FileSystemAuditRule("Everyone", "Read, Write", "ContainerInherit,ObjectInherit", "None", "Success, Failure")
$acl = Get-Acl "C:\SecureFolder"
$acl.AddAuditRule($AuditRule)
Set-Acl -Path "C:\SecureFolder" -AclObject $acl

# Récupérer les événements d’audit et les exporter vers un fichier CSV
$events = Get-WinEvent -LogName Security | Where-Object { $_.Id -eq 4663 -and $_.Properties[5].Value -like "*C:\SecureFolder*" }
$events | Select-Object TimeCreated, @{Name="User"; Expression={$_.Properties[1].Value}}, @{Name="File Accessed"; Expression={$_.Properties[5].Value}}, @{Name="Access Mask"; Expression={$_.Properties[8].Value}} | Export-Csv -Path "C:\SecureFolderAudit.csv" -NoTypeInformation

# Configuration du client pour rejoindre le domaine
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"
Add-Computer -DomainName "cafe.local" -Credential (Get-Credential) -Restart

# Vérification de la connexion du client au domaine
(Get-WmiObject Win32_ComputerSystem).Domain

# Vérification de la version SMB sur le serveur
Get-SmbServerConfiguration | Select-Object EnableSMB1Protocol, EnableSMB2Protocol

# Désactiver SMB 2.0 (si requis)
Set-SmbServerConfiguration –EnableSMB2Protocol $false

# Activer uniquement les connexions chiffrées pour SMB
Set-SmbServerConfiguration -RejectUnencryptedAccess $true

# Liste des partages SMB actifs avec leur état de chiffrement
Get-SmbShare | Select-Object Name, Path, EncryptData

# Activer le journal d’audit avancé pour les connexions SMB
auditpol /set /subcategory:"Logon" /success:enable /failure:enable

# Filtrer les événements d'audit relatifs aux connexions SMB
$logEvents = Get-WinEvent -LogName "Security" | Where-Object { $_.Id -eq 5140 }
$logEvents | Export-Csv -Path "C:\SMBConnectionAudit.csv" -NoTypeInformation
```

### **2. Script pour joindre une machine Windows 10 au domaine**

Sur votre machine Windows 10, vous devez exécuter le script **`joinDomain.ps1`** pour configurer la machine et la joindre automatiquement au domaine **cafe.local**.

#### **Contenu du script :**

```powershell
# Configurer l'adresse DNS pour pointer vers le serveur AD DS
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.168.1.10"

# Joindre la machine au domaine
Add-Computer -DomainName "cafe.local" -Credential (Get-Credential) -Restart
```

---

## **Étapes d'exécution**

### **1. Exécuter le script sur le serveur Windows Server**

1. Ouvrez **PowerShell** en tant qu'administrateur sur le serveur.
2. Téléchargez ou copiez le fichier **`setupADSMB.ps1`** sur votre serveur.
3. Exécutez le script en tapant :
   ```powershell
   .\setupADSMB.ps1
   ```

### **2. Exécuter le script sur la machine Windows 10**

1. Ouvrez **PowerShell** en tant qu'administrateur sur la machine Windows 10.
2. Téléchargez ou copiez le fichier **`joinDomain.ps1`** sur votre machine Windows 10.
3. Exécutez le script en tapant :
   ```powershell
   .\joinDomain.ps1
   ```

### **3. Vérification**

Après l'exécution des scripts, vérifiez que :
- Le serveur Windows est configuré avec le domaine **cafe.local**.
- Le dossier **SecureFolder** est partagé avec chiffrement SMB activé.
- La machine Windows 10 a bien rejoint le domaine.
- Les audits sont actifs pour surveiller les connexions SMB et les accès au partage.

---

## **Conclusion**

En suivant ce guide, vous serez en mesure d'automatiser la création d'un domaine Active Directory, d'ajouter des partages avec chiffrement SMB 3.1.1, de configurer un audit complet et d'intégrer des machines Windows 10 au domaine **cafe.local**. Vous pouvez réutiliser ces scripts pour toute autre configuration similaire sur d'autres serveurs et clients.
