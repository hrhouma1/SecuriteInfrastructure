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
auditpol /set /category:"Object Access" /success:enable /failure:enable

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

