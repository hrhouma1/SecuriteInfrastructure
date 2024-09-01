# SMB et Sécurisation du Trafic SMB

---

## **Introduction à SMB (Server Message Block)**

# **1. Définition de SMB**

**Qu'est-ce que SMB ?**
- SMB, ou **Server Message Block**, est un protocole de communication réseau utilisé principalement dans les systèmes Windows pour le partage de fichiers, d'imprimantes, de ports série et autres ressources entre les ordinateurs d'un réseau local (LAN). Il permet également aux ordinateurs de faire des requêtes pour accéder à des fichiers sur un serveur distant, ainsi que pour d'autres services tels que l'impression sur une imprimante réseau.

**Origine et Histoire :**
- SMB a été initialement développé par IBM dans les années 1980 pour DOS afin de permettre le partage de fichiers. Microsoft a ensuite largement adopté et étendu ce protocole pour l'inclure dans son système d'exploitation Windows, devenant un composant essentiel des environnements réseau Microsoft.

**Fonctionnement de base :**
- SMB fonctionne principalement au niveau de la couche application (couche 7) du modèle OSI, sur des protocoles de transport comme NetBIOS sur TCP/IP (NBT) ou TCP direct. Il permet aux clients (ordinateurs utilisateurs) de se connecter à un serveur SMB, de demander des ressources (comme un fichier ou une imprimante), et de les utiliser comme s'ils étaient sur leur propre machine.

---

# **2. Versions de SMB**

**2.1 SMBv1 (SMB 1.0)**

- **Introduction :**
  - SMBv1 est la première version du protocole SMB, développée à l'origine par IBM. Elle a été intégrée dans les premières versions de Windows comme Windows NT 4.0, Windows 95, et Windows 98.
  
- **Caractéristiques principales :**
  - **Connexion par "raccourci" (DFS) :** SMBv1 permet aux utilisateurs d'accéder à des fichiers via des raccourcis réseau, grâce à la prise en charge des DFS (Distributed File System).
  - **Fonctionnalités limitées de sécurité :** SMBv1 ne prend pas en charge les fonctionnalités de sécurité avancées telles que le chiffrement et la signature des paquets, ce qui le rend vulnérable aux attaques.

- **Problèmes de sécurité :**
  - **Failles exploitables :** SMBv1 est sujet à plusieurs vulnérabilités, notamment celles exploitées par des ransomwares comme WannaCry. Ces failles permettent aux attaquants d'exécuter du code malveillant sur des machines distantes, de crypter des fichiers, et de propager des infections sur tout le réseau.
  - **Absence de mécanismes modernes de sécurité :** En raison de son ancienneté, SMBv1 ne prend pas en charge les techniques de sécurité modernes telles que la signature et le chiffrement des paquets, ce qui permet des attaques de type "man-in-the-middle".

- **Statut actuel :**
  - Microsoft recommande vivement de désactiver SMBv1 dans tous les environnements modernes. Cette version est obsolète et désactivée par défaut dans les versions récentes de Windows.

**2.2 SMBv2 (SMB 2.0)**

- **Introduction :**
  - SMBv2 a été introduit avec Windows Vista et Windows Server 2008. Cette version a été entièrement réécrite pour surmonter les limitations de SMBv1, avec des améliorations significatives en termes de performance et de sécurité.

- **Caractéristiques principales :**
  - **Amélioration de la performance :** SMBv2 réduit la surcharge réseau en combinant plusieurs commandes en un seul paquet. Cela diminue le nombre de requêtes réseau nécessaires pour effectuer des opérations, améliorant ainsi la performance globale.
  - **Support pour la signature des paquets :** SMBv2 introduit la signature des paquets, qui permet de garantir que les données échangées n'ont pas été modifiées en transit. Cela aide à prévenir les attaques "man-in-the-middle".
  - **Meilleure gestion des sessions :** SMBv2 gère les sessions de manière plus efficace, réduisant ainsi la latence et optimisant l'utilisation des ressources réseau.

- **Sécurité :**
  - **Signature des paquets :** SMBv2 inclut une option pour signer les paquets, ce qui ajoute une couche de sécurité en vérifiant que les données reçues n'ont pas été altérées.
  - **Suppression des commandes obsolètes :** Cette version supprime plusieurs commandes obsolètes et peu sécurisées, ce qui simplifie le protocole et réduit les possibilités d'exploitation.

**2.3 SMBv3 (SMB 3.0 et 3.1.1)**

- **Introduction :**
  - SMBv3 a été introduit avec Windows 8 et Windows Server 2012. SMB 3.1.1, la version la plus récente, a été lancée avec Windows 10 et Windows Server 2016. SMBv3 est une évolution significative du protocole SMB avec un accent particulier sur la sécurité et la performance.

- **Caractéristiques principales :**
  - **Chiffrement des données :** SMBv3 permet de chiffrer les données en transit entre le client et le serveur. Cela empêche les attaquants d’intercepter et de lire les données transférées sur le réseau.
  - **SMB Direct (RDMA) :** SMBv3 prend en charge RDMA (Remote Direct Memory Access), ce qui permet des transferts de données à faible latence avec une utilisation minimale du CPU. Ceci est particulièrement utile dans les environnements de datacenter.
  - **SMB Multichannel :** SMBv3 permet l'utilisation de plusieurs connexions réseau pour la même session SMB, améliorant ainsi la tolérance aux pannes et la bande passante disponible.
  - **Signature des paquets obligatoire :** SMBv3 renforce la sécurité en exigeant que toutes les communications soient signées, empêchant les modifications non autorisées des données.

- **Sécurité :**
  - **Chiffrement de bout en bout :** SMBv3 introduit un chiffrement de bout en bout, qui protège les données en transit contre les attaques de type "man-in-the-middle".
  - **Signature des paquets renforcée :** La signature des paquets est obligatoire, ce qui signifie que chaque paquet est vérifié pour s'assurer qu'il n'a pas été altéré.

---

# **3. Menaces et Vulnérabilités Associées à SMB**

**3.1 Attaques Exploitant SMBv1**

- **Ransomware :**
  - Les ransomwares comme WannaCry et NotPetya ont utilisé les vulnérabilités de SMBv1 pour se propager rapidement à travers les réseaux. Ils exploitent la capacité de SMBv1 à exécuter du code malveillant sur des machines distantes, chiffrant les fichiers des utilisateurs et exigeant une rançon en échange de la clé de décryptage.

- **Propagation des Vers :**
  - Les vers informatiques, comme Conficker, exploitent SMBv1 pour se propager sans intervention humaine. Ils scannent le réseau à la recherche d'autres machines vulnérables, y installent du code malveillant, et continuent ainsi à infecter d'autres systèmes.

**3.2 Attaques "Man-in-the-Middle"**

- **Interceptation des Données :**
  - Sans chiffrement, SMBv1 et SMBv2 (sans signature activée) permettent aux attaquants d'intercepter les communications entre le client et le serveur. Ces attaques, connues sous le nom d'attaques "man-in-the-middle", peuvent permettre à un attaquant de lire ou même de modifier les données en transit.

- **Redirection de Connexions :**
  - Les attaquants peuvent utiliser des techniques comme le DNS Spoofing pour rediriger les connexions SMB vers un serveur malveillant. Une fois redirigées, les données sensibles échangées peuvent être interceptées, modifiées, ou utilisées pour mener d'autres attaques.

**3.3 Exploits de Force Brute**

- **Forçage de Mot de Passe :**
  - Les attaquants peuvent tenter de forcer les mots de passe des utilisateurs pour accéder à des partages SMB non sécurisés. Une fois qu'ils obtiennent un accès, ils peuvent voler des informations sensibles, installer des logiciels malveillants, ou utiliser l'accès pour pivoter vers d'autres systèmes sur le réseau.

---

# **4. Sécurisation du Trafic SMB**

**4.1 Désactivation de SMBv1**

- **Pourquoi désactiver SMBv1 ?**
  - SMBv1 est obsolète et ne fournit pas les mécanismes de sécurité nécessaires pour protéger les systèmes modernes. En désactivant SMBv1, vous éliminez une surface d'attaque majeure qui peut être exploitée par des malwares, des ransomwares, et d'autres types d'attaques réseau.

- **Conséquences de la désactivation :**
  - Une fois SMBv1 désactivé, les systèmes qui dépendent encore de ce protocole pour fonctionner (comme les très anciennes versions de Windows ou certains équipements obsolètes) ne pourront plus communiquer via SMB. Il est donc essentiel de s'assurer que tous les systèmes dans l'environnement sont compatibles avec SMBv2 ou SMBv3 avant de procéder.

**4.2 Activation et Utilisation de SMBv2 et SMBv3**

- **Avantages de SMBv2 :**
  - **Efficacité :** SMBv2 est beaucoup plus efficace que SMBv1, nécessitant moins de bande passante pour effectuer les

 mêmes tâches. Cela réduit la latence réseau et améliore la performance des applications qui utilisent SMB.
  - **Sécurité :** SMBv2 introduit la signature des paquets, empêchant ainsi les modifications non autorisées des données en transit.

- **Avantages de SMBv3 :**
  - **Chiffrement :** SMBv3 permet de chiffrer les données échangées entre les clients et les serveurs, rendant les attaques "man-in-the-middle" beaucoup plus difficiles.
  - **SMB Direct :** SMBv3 prend en charge RDMA, ce qui permet des transferts de données plus rapides avec une utilisation réduite du CPU, idéal pour les environnements virtualisés ou les datacenters.
  - **Multichannel :** SMBv3 supporte plusieurs connexions réseau simultanées pour la même session SMB, ce qui améliore la tolérance aux pannes et la bande passante.

**4.3 Signature des Paquets SMB**

- **Qu'est-ce que la signature des paquets ?**
  - La signature des paquets est un mécanisme de sécurité qui permet de vérifier que les données envoyées par le client et le serveur n'ont pas été altérées pendant leur transit. Chaque paquet SMB est accompagné d'une signature numérique qui est vérifiée par le destinataire.

- **Pourquoi l'activer ?**
  - Activer la signature des paquets SMB aide à prévenir les attaques "man-in-the-middle", où un attaquant pourrait intercepter et modifier les données échangées entre le client et le serveur.

**4.4 Chiffrement du Trafic SMB**

- **Pourquoi chiffrer ?**
  - Le chiffrement du trafic SMB empêche les attaquants d'intercepter et de lire les données échangées entre le client et le serveur. Cela est particulièrement important pour les environnements où des informations sensibles, telles que des données financières ou des informations d'identification, sont échangées.

- **Comment ça fonctionne ?**
  - Lorsqu'il est activé, SMBv3 utilise AES (Advanced Encryption Standard) pour chiffrer les données avant qu'elles ne soient envoyées sur le réseau. Seuls le client et le serveur possédant la clé de chiffrement peuvent déchiffrer et lire les données.

**4.5 Auditer les Connexions SMB**

- **Importance de l'audit :**
  - L'audit des connexions SMB permet aux administrateurs de surveiller les activités réseau suspectes, telles que les tentatives de connexion non autorisées ou les accès répétitifs avec des informations d'identification incorrectes. Cela aide à identifier rapidement les menaces potentielles et à prendre des mesures correctives.

- **Que rechercher ?**
  - Les administrateurs doivent surveiller les connexions SMB qui proviennent de sources inconnues ou suspectes, les tentatives de connexion échouées, et les accès non autorisés aux partages réseau.

**4.6 Utilisation de Group Policy pour Renforcer la Sécurité SMB**

- **Gestion centralisée :**
  - Dans les environnements Active Directory, les administrateurs peuvent utiliser les stratégies de groupe (Group Policy) pour appliquer des paramètres de sécurité SMB à tous les ordinateurs d'un domaine. Cela inclut la désactivation de SMBv1, l'exigence de la signature des paquets, et l'activation du chiffrement.

- **Exemple d'application :**
  - Une politique de groupe pourrait être configurée pour s'assurer que toutes les connexions SMB dans le domaine utilisent SMBv3 avec le chiffrement activé. Cela garantit que même si un ordinateur n'est pas correctement configuré, il respectera les politiques de sécurité définies par l'administrateur du domaine.

---

# **Conclusion**

- SMB est un protocole essentiel pour le partage de ressources dans les environnements réseau, mais il peut également représenter un risque de sécurité important s'il n'est pas correctement configuré.
- L'utilisation de versions obsolètes comme SMBv1 expose les systèmes à des attaques graves, tandis que les versions plus récentes comme SMBv3 offrent des fonctionnalités de sécurité avancées telles que le chiffrement et la signature des paquets.
- Pour sécuriser efficacement un environnement réseau utilisant SMB, il est crucial de désactiver SMBv1, d'activer SMBv2 et SMBv3, et d'appliquer des mesures de sécurité supplémentaires telles que le chiffrement des connexions et l'audit des activités réseau. L'utilisation des stratégies de groupe dans les environnements Active Directory permet de centraliser et de renforcer la sécurité à l'échelle de l'entreprise.
- Ce cours vous a permis de comprendre les différentes versions de SMB, les risques associés à chaque version, et les mesures à prendre pour sécuriser les connexions SMB dans un environnement réseau. Vous êtes maintenant prêt à appliquer ces connaissances dans un cadre pratique pour renforcer la sécurité de vos systèmes.



----
----
----
---

## **Annexe 1 : Commandes PowerShell pour Gérer SMB**

### **1. Vérification et Désactivation de SMBv1**

**Vérifier si SMBv1 est activé :**
- Cette commande vérifie si le protocole SMBv1 est activé sur votre serveur ou machine client.
```powershell
Get-SmbServerConfiguration | Select EnableSMB1Protocol
```

**Résultat attendu :**
- La sortie sera `True` ou `False`, indiquant si SMBv1 est activé (`True`) ou désactivé (`False`).

**Désactiver SMBv1 :**
- Désactivez SMBv1 pour renforcer la sécurité et prévenir les vulnérabilités associées.
```powershell
Set-SmbServerConfiguration -EnableSMB1Protocol $false
```

**Vérifier à nouveau après désactivation :**
- Re-vérifiez que SMBv1 est désactivé.
```powershell
Get-SmbServerConfiguration | Select EnableSMB1Protocol
```

### **2. Activation et Configuration de SMBv2 et SMBv3**

**Vérifier si SMBv2 est activé :**
- Cette commande vérifie si SMBv2 est activé sur votre machine.
```powershell
Get-SmbServerConfiguration | Select EnableSMB2Protocol
```

**Activer SMBv2 :**
- Activez SMBv2 pour garantir une meilleure sécurité et performance réseau.
```powershell
Set-SmbServerConfiguration -EnableSMB2Protocol $true
```

**Vérifier si SMBv3 est activé :**
- Cette commande montre la version de SMB utilisée dans les connexions actives.
```powershell
Get-SmbConnection | Select Dialect
```

**Activer le chiffrement SMBv3 :**
- Le chiffrement SMBv3 protège les données en transit contre les interceptions non autorisées.
```powershell
Set-SmbServerConfiguration -EncryptData $true
```

**Exiger la signature des paquets SMBv3 :**
- La signature des paquets SMBv3 garantit que les données ne sont pas altérées pendant le transfert.
```powershell
Set-SmbServerConfiguration -RequireSecuritySignature $true
```

**Vérifier les paramètres de configuration SMBv3 :**
- Assurez-vous que le chiffrement et la signature sont activés pour SMBv3.
```powershell
Get-SmbServerConfiguration | Select EncryptData, RequireSecuritySignature
```

---

## **Annexe 2 : Commandes PowerShell pour Auditer SMB**

### **1. Auditer les Connexions et Journaux SMB**

**Lister les connexions SMB actives :**
- Cette commande affiche les connexions SMB en cours, avec des détails sur les partages accédés, les utilisateurs connectés, et les protocoles utilisés.
```powershell
Get-SmbConnection
```

**Auditer les journaux d'événements SMB :**
- Affichez les journaux d'événements liés à SMB pour analyser les connexions, les erreurs, et les incidents de sécurité.
```powershell
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit
```

### **2. Commandes pour Auditer les Tentatives de Connexion SMB**

**Activer l’audit des connexions SMB :**
- Cette commande active l’audit des connexions SMB, enregistrant les tentatives de connexion réussies et échouées.
```powershell
Set-SmbServerConfiguration -AuditSmbSessionStateChange $true
```

**Vérifier l’audit des connexions SMB :**
- Utilisez cette commande pour examiner les journaux d’audit SMB, notamment les tentatives de connexion rejetées.
```powershell
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit | Select TimeCreated, Message
```

### **3. Commandes pour Protéger les Connexions SMB**

**Rejeter les connexions non chiffrées (SMBv3) :**
- Cette commande force le serveur à rejeter toutes les connexions SMB non chiffrées.
```powershell
Set-SmbServerConfiguration -RejectUnencryptedAccess $true
```

**Vérifier les connexions rejetées :**
- Consultez les journaux pour voir quelles connexions SMB ont été rejetées en raison du manque de chiffrement.
```powershell
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Audit | Where-Object {$_.Message -like "*Rejected*"}
```

---

## **Annexe 3 : Commandes PowerShell pour Configurer SMB via Group Policy**

### **1. Désactivation de SMBv1 via Group Policy**

**Configurer les stratégies de groupe pour désactiver SMBv1 (serveur) :**
- Créez une entrée de registre via Group Policy pour désactiver SMBv1 sur tous les serveurs du domaine.
```powershell
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" -Name SMB1 -Value 0 -PropertyType DWORD -Force
```

**Configurer les stratégies de groupe pour désactiver SMBv1 (client) :**
- Désactivez SMBv1 sur tous les clients via une entrée de registre.
```powershell
New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" -Name SMB1 -Value 0 -PropertyType DWORD -Force
```

### **2. Activation de SMB Signing via Group Policy**

**Activer SMB Signing pour les serveurs :**
- Configurez SMB Signing pour les serveurs afin d'exiger la signature des paquets, empêchant toute modification non autorisée des données en transit.
```powershell
Set-SmbServerConfiguration -RequireSecuritySignature $true
```

**Activer SMB Signing pour les clients :**
- Cette commande exige que les clients utilisent SMB Signing, ajoutant une couche de sécurité supplémentaire.
```powershell
Set-SmbClientConfiguration -RequireSecuritySignature $true
```

### **3. Configuration de SMB Encryption via Group Policy**

**Configurer SMB Encryption sur tous les partages :**
- Activez le chiffrement SMB pour toutes les connexions afin de protéger les données sensibles contre les attaques.
```powershell
Set-SmbServerConfiguration -EncryptData $true
```

### **4. Auditer les Modifications de Configuration SMB**

**Auditer les modifications dans les configurations SMB :**
- Suivez les changements apportés aux configurations SMB en temps réel pour garantir que les bonnes pratiques de sécurité sont respectées.
```powershell
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Operational
```

---

## **Annexe 4 : Commandes PowerShell pour Gérer les Partages SMB et Analyser le Réseau**

### **1. Gérer les Partages SMB**

**Lister tous les partages SMB disponibles :**
- Affichez la liste de tous les partages SMB configurés sur le serveur.
```powershell
Get-SmbShare
```

**Créer un nouveau partage SMB :**
- Créez un partage SMB pour permettre l’accès à un répertoire spécifique sur le réseau.
```powershell
New-SmbShare -Name "PartageDocuments" -Path "C:\Documents" -FullAccess "Administrateurs"
```

**Supprimer un partage SMB :**
- Supprimez un partage SMB existant si vous n’en avez plus besoin ou s’il représente un risque de sécurité.
```powershell
Remove-SmbShare -Name "PartageDocuments"
```

### **2. Analyser les Connexions Réseau et la Performance SMB**

**Vérifier les connexions SMB actives et les performances :**
- Analysez les connexions actives pour identifier les clients connectés, les fichiers ouverts, et la performance des transferts SMB.
```powershell
Get-SmbSession
```

**Détecter les erreurs de connexion SMB :**
- Identifiez les erreurs ou les tentatives de connexion échouées qui pourraient indiquer des problèmes de configuration ou des attaques potentielles.
```powershell
Get-WinEvent -LogName Microsoft-Windows-SMBServer/Operational | Where-Object {$_.LevelDisplayName -eq "Error"}
```

### **3. Configuration des Règles de Pare-feu pour SMB**

**Ouvrir les ports SMB dans le pare-feu :**
- Assurez-vous que les ports SMB nécessaires (445 et 139) sont ouverts pour permettre le bon fonctionnement des partages SMB.
```powershell
New-NetFirewallRule -DisplayName "Allow SMB Inbound" -Direction Inbound -Protocol TCP -LocalPort 445,139 -Action Allow
```

**Restreindre l’accès aux partages SMB :**
- Limitez l'accès aux partages SMB à des plages IP spécifiques pour réduire la surface d'attaque.
```powershell
New-NetFirewallRule -DisplayName "Allow SMB from Specific Subnet" -Direction Inbound -Protocol TCP -LocalPort 445,139 -RemoteAddress 192.168.1.0/24 -Action Allow
```
