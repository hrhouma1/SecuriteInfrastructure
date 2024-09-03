# Sécurisation et analyse du trafic SMB

Windows Server 2016 et Windows 10 (et ultérieur) incluent de nombreuses améliorations de la sécurité de SMB.

Dans ce chapitre, vous découvrirez les modifications apportées à la sécurité dans SMB 3.1.1. Vous découvrirez aussi la configuration requise pour le chiffrement SMB 3.1.1 et comment configurer le chiffrement SMB sur les partages SMB.

--------
# 1 - Objectifs de la leçon
--------

À la fin de ce chapitre, vous serez à même de :

- Décrire la sécurité du protocole SMB 3.1.1
- Décrire la configuration requise pour implémenter SMB 3.1.1
- Décrire comment configurer le chiffrement SMB sur des partages SMB
- Décrire comment désactiver SMB 1.0
- Désactiver SMB 1.0, puis activer le chiffrement SMB

--------
# 2 - Présentation de la sécurité du protocole SMB 3.1.1
--------

Le chiffrement SMB 3.0.x garantit la confidentialité des paquets de données et permet d’empêcher un hacker malveillant de falsifier ou d’espionner les paquets de données. L’intégrité de pré-authentification est une des nouvelles améliorations de sécurité de SMB 3.1.1 dans Windows Server 2016 et Windows 10 (et ultérieur). La sécurité dans SMB 3.1.1 est constituée de deux parties : l’intégrité de pré-authentification et le chiffrement SMB.

# Intégrité de pré-authentification

L’intégrité de pré-authentification est une fonctionnalité obligatoire dans SMB 3.1.1. Au cours de l’établissement d’une session, les messages « negotiate » et « session setup » sont protégés par un hachage (SHA-512) fort.

Le client et le serveur approuvent mutuellement la connexion et les propriétés de la session ce qui améliore la protection contre une attaque de l’intercepteur (man in the middle) qui falsifie la connexion.

--------
# 3 - Améliorations du chiffrement SMB
--------

Windows Server 2016 et Windows 10 (et ultérieur) conservent la prise en charge d’AES-128-CCM pour la compatibilité de SMB 3.0.x.

SMB 3.1.1 introduit la prise en charge de la méthode de chiffrement authentifié AES-128-GCM. Les tests montrent que le mode AES-128-GCM offre une amélioration significative des performances en comparaison d’AES-128-CCM, et que ce gain va jusqu’à doubler lors de la copie de fichiers volumineux via une connexion SMB 3.1.1 chiffrée.

--------
# 4 - Résumé
--------

- SMB 3.0.x fournit un chiffrement du trafic SMB.
- SMB 3.1.1 ajoute une intégrité de pré-authentification.
- La pré-authentification hache et signe de façon numérique les messages de négociation et de configuration de session :
  - Une falsification entraînerait un échec de connexion.
- Le chiffrement permet de s’assurer que la session n’est pas falsifiée.

# Configuration requise pour le chiffrement SMB 3.1.1

Windows 10 et Windows Server 2016 (et ultérieur) introduisent SMB 3.1.1. Cependant, ces systèmes d’exploitation sont compatibles avec les versions antérieures de SMB et peuvent communiquer avec des systèmes plus anciens. Pour utiliser SMB 3.1.1, vos systèmes Windows 10 ou Windows Server 2016 (et ultérieur) doivent communiquer avec d’autres systèmes Windows 10 ou Windows Server 2016 (et ultérieur).

---
# 5 - Tableau de compatibilité SMB
----

```
+-----------------------------------+--------------------------+-------------------------+
|  Système d'exploitation           |  Version SMB             |  Version SMB            |
|                                   |  Windows 10/Windows       |  Windows 8.1/Windows    |
|                                   |  Server 2016             |  Server 2012 R2         |
+-----------------------------------+--------------------------+-------------------------+
|  Windows 10/Windows Server 2016   |  SMB 3.1.1               |  SMB 3.02               |
+-----------------------------------+--------------------------+-------------------------+
|  Windows 8.1/Windows Server 2012  |  SMB 3.02                |  SMB 3.0                |
+-----------------------------------+--------------------------+-------------------------+
|  Windows 8/Windows Server 2012    |  SMB 3.0                 |  SMB 2.1                |
+-----------------------------------+--------------------------+-------------------------+
|  Windows 7/Windows Server 2008    |  SMB 2.1                 |  SMB 2.0                |
|  R2                               |                          |                         |
+-----------------------------------+--------------------------+-------------------------+
|  Windows Vista/Windows Server     |  SMB 2.0.2               |  SMB 1.x                |
|  2008                             |                          |                         |
+-----------------------------------+--------------------------+-------------------------+
```
-----
# 6 - Configuration du chiffrement SMB sur les partages SMB
------

L’activation du chiffrement SMB restreint le partage de fichiers ou le serveur de fichiers aux seuls clients SMB 3.x pour les partages protégés. Vous pouvez activer le chiffrement SMB en utilisant Windows PowerShell ou le Gestionnaire de serveur.

Pour utiliser Windows PowerShell pour activer le chiffrement SMB sur un partage de fichiers existant sur un serveur qui héberge le partage, ouvrez une invite de commandes Windows PowerShell, tapez l’applet de commande suivante, puis appuyez sur Entrée :

```shell
Set-SmbShare –Name <nom_partage> –EncryptData $true
```

Pour chiffrer tous les partages sur un serveur de fichiers, à une invite de commandes Windows PowerShell sur le serveur, tapez l’applet de commande suivante, puis appuyez sur Entrée :

```shell
Set-SmbServerConfiguration –EncryptData $true
```

Pour simultanément créer un partage SMB et activer le chiffrement SMB, ouvrez une invite de commandes Windows PowerShell, tapez l’applet de commande suivante, puis appuyez sur Entrée :

```shell
New-SmbShare –Name <nom_partage> –Path <nom_chemin> –EncryptData $true
```

-------------
# 7 - Gestionnaire de serveur
--------

Dans le Gestionnaire de serveur, Services de fichiers et de stockage, vous pouvez activer le chiffrement sur un partage via la page Gestion de partages :

1. Cliquez avec le bouton droit sur le partage approprié, puis cliquez sur **Propriétés**.
2. Dans la page **Paramètres**, cliquez sur **Chiffrer l’accès aux données**.

Pour autoriser des connexions qui n’utilisent pas le chiffrement SMB 3, par exemple quand des serveurs et des clients plus anciens se trouvent encore dans votre réseau, ouvrez une invite de commandes Windows PowerShell, tapez la commande suivante, puis appuyez sur Entrée :

```shell
Set-SmbServerConfiguration –RejectUnencryptedAccess $false
```

----------
# 8 - Désactivation de SMB 1.0
-----------

Pour garantir que les clients SMB 3.x utilisent toujours le chiffrement pour accéder à un partage chiffré, vous devez désactiver SMB 1.x, que toutes les versions des systèmes d’exploitation client et serveur Windows incluent pour la compatibilité descendante. SMB 1.x est un protocole plus ancien, qui expose une surface d’attaque étendue et sans doute non nécessaire. (SMB 1.x prend en charge plus de 100 commandes du protocole.)

Vous pouvez désactiver SMB 1.x sur les systèmes Windows 7, Windows Server 2008 R2, Windows Vista et Windows Server 2008 en modifiant le Registre ou en exécutant la commande Windows PowerShell 2.0 suivante :

```shell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 -Force
```

Vous pouvez désactiver SMB 1.x dans les systèmes Windows 8 et ultérieurs, ou Windows Server 2012 et ultérieurs en utilisant la commande suivante :

```shell
Set-SmbServerConfiguration –EnableSMB1Protocol $false
```

Vous pouvez désinstaller SMB 1 de Windows 8.1 et ultérieur en utilisant l’applet de commande suivante :

```shell
Remove-WindowsFeature FS-SMB1
```

Dans Windows 10 ou Windows Server 2016 (et ultérieur), vous pouvez activer l’audit du trafic SMB 1.x en utilisant l’applet de commande suivante :

```shell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

Pour afficher les événements d’audit générés, vous pouvez utiliser l’applet de commande suivante :

```shell
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
```
