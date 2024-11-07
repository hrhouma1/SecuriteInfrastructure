-----------------------------
# Introduction 
-----------------------------

- *Ce cours détaillé vous guide de manière exhaustive pour comprendre et configurer un cluster NLB sous Windows Server 2016/2019. Vous savez maintenant comment fonctionne la répartition de charge, comment configurer les serveurs et les DNS, et comment tester la disponibilité du service.*
- *Comprenons en détail comment fonctionne le **Network Load Balancer (NLB)** et le processus complet du cheminement des requêtes des utilisateurs jusqu'aux serveurs dans un cluster NLB. Ce guide étape par étape comprend le fonctionnement du NLB, les configurations nécessaires, et un schéma détaillé du workflow de communication.*


## Introduction : Qu'est-ce que le NLB et Comment ça Fonctionne ?

Un **Network Load Balancer (NLB)** est une technologie utilisée pour distribuer le trafic réseau entre plusieurs serveurs. En d'autres termes, il permet de :

1. **Répartir la charge** : En dirigeant les requêtes réseau vers plusieurs serveurs, le NLB empêche la surcharge d'un seul serveur. Chaque requête est distribuée entre les serveurs du cluster.
  
2. **Assurer la haute disponibilité** : Si un serveur tombe en panne, le NLB redirige automatiquement le trafic vers les autres serveurs encore actifs, assurant la continuité du service pour les utilisateurs.

3. **Utiliser une adresse IP virtuelle (VIP)** : Le NLB utilise une adresse IP virtuelle (VIP) pour le cluster. Les utilisateurs se connectent à cette VIP, et le NLB dirige les requêtes vers un serveur disponible.

### Composants dans notre configuration NLB

Pour ce laboratoire, le cluster est composé de :
- **3 serveurs (nœuds)** avec une configuration réseau spécifique.
- **1 adresse IP virtuelle (VIP)** qui représente le cluster entier et que les utilisateurs utilisent pour se connecter.
- **1 serveur DNS** pour la résolution de noms (convertir le nom de domaine en adresse IP du cluster).

---

## Étape 1 : Comprendre le Cheminement d'une Requête dans un Cluster NLB

Voici le flux de communication, de la machine de l’utilisateur jusqu'aux serveurs du cluster.

1. **L'utilisateur entre une URL** dans son navigateur, par exemple `http://www.test.local`.
   
2. **Le navigateur de l'utilisateur consulte le DNS** :
   - Le DNS traduit `www.test.local` en **192.168.0.111** (l'adresse IP virtuelle du cluster).

3. **La requête est envoyée à la VIP (192.168.0.111)** :
   - Cette IP est gérée par le NLB, qui distribue les requêtes entrantes vers les serveurs disponibles.

4. **Le NLB sélectionne un serveur** :
   - Le NLB utilise un algorithme de répartition (ex. : Round Robin) pour décider vers quel serveur diriger la requête.
   - Il choisit un serveur actif parmi les trois serveurs du cluster.

5. **Le serveur répond à l’utilisateur** :
   - Une fois la requête traitée, le serveur renvoie la réponse directement à l'utilisateur.

### Schéma de Cheminement de la Requête

```
Utilisateur            Internet               Switch              Cluster NLB
+-----------+           +------>        +---------------+       +------------------+
|           |           |               |               |       |                  |
|  Browser  |  DNS      |               |   Switch      |       |  VIP: 192.168.0.111
|  http://www.test.local -->    192.168.0.111           -->   |        NLB      |
|           |-----------+               |               |       |    Serveurs:   |
|           |           |               |               |       |  1. Win2012ND1 |
+-----------+                           +---------------+       |  2. Win2012ND2 |
        |                                                       |  3. WinCoreND3 |
        |___________________________Répartition________________________|
                                 du Trafic
```

---

## Étape 2 : Configuration Pas à Pas avec PowerShell

### 1. Configuration du Serveur Utilisateur

Lorsque l'utilisateur entre `http://www.test.local` dans son navigateur :
- Assurez-vous que `test.local` est correctement configuré dans le DNS et pointe vers la VIP `192.168.0.111`.

### 2. Configuration du DNS

Le DNS joue un rôle crucial en associant un nom convivial (`www.test.local`) à l'adresse IP virtuelle du cluster NLB. Voici comment le configurer :

```powershell
Add-DnsServerResourceRecordA -Name "ClusterWeb" -ZoneName "test.local" -IPv4Address "192.168.0.111"
Add-DnsServerResourceRecordCName -Name "www" -ZoneName "test.local" -HostNameAlias "ClusterWeb.test.local"
```

### 3. Configuration des Cartes Réseau sur Chaque Serveur

Chaque serveur du cluster a deux cartes réseau :
- **Carte de gestion** : Permet l'administration du serveur.
- **Carte NLB** : Utilisée pour la répartition de charge.

Sur chaque serveur :
- Configurez une **IP de gestion** unique, par exemple `192.168.0.1`, `192.168.0.2`, etc.
- Configurez une **IP NLB** unique pour le trafic NLB, par exemple `192.168.0.11`, `192.168.0.22`, etc.

Exemple de configuration pour `WinCoreND3` :
```powershell
# IP pour la gestion
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.0.3 -PrefixLength 24

# IP pour le trafic NLB
New-NetIPAddress -InterfaceAlias "Ethernet 2" -IPAddress 192.168.0.33 -PrefixLength 24
```

### 4. Création du Cluster NLB

Sur un des serveurs du cluster (par exemple `Win2012ND1`), créez le cluster avec l’adresse VIP.

```powershell
New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
```

### 5. Ajout de Serveurs au Cluster NLB

Ajoutez chaque serveur au cluster en spécifiant un identifiant unique :

```powershell
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "WinCoreND3.test.local" -UniqueId 3
```

### 6. Vérification des Nœuds dans le Cluster

Vérifiez que tous les serveurs sont bien ajoutés au cluster :

```powershell
Get-NlbClusterNode -ClusterName "ClusterWeb"
```

---

## Test et Vérification

1. **Tester l'accès à partir de la machine client (WinCLI)** :
   - Dans le navigateur, tapez `http://www.test.local`.
   - La requête est envoyée au NLB, qui la redirige vers un des serveurs du cluster.

2. **Tester la redondance** :
   - Arrêtez un ou deux serveurs pour simuler une panne, puis accédez de nouveau à `http://www.test.local`.
   - Le NLB doit rediriger automatiquement les requêtes vers les serveurs restants.

3. **Vérifier les connexions avec un Script PowerShell**

Vous pouvez utiliser un script PowerShell pour vérifier que chaque nœud du cluster est en ligne.

```powershell
$nodes = @("Win2012ND1", "Win2012ND2", "WinCoreND3")

foreach ($node in $nodes) {
    if (Test-Connection -ComputerName $node -Count 2 -Quiet) {
        Write-Host "$node est en ligne"
    } else {
        Write-Host "$node est hors ligne"
    }
}
```

---

## Schéma Final avec le Workflow Complet

```
                           Utilisateur
                             |
                             |
                    +----------------------+
                    | Entrez l'URL         |
                    | http://www.test.local|
                    +----------+-----------+
                               |
                               | DNS résout l'URL
                               |
                               V
                       +---------------+       
                       |  VIP (192.168.0.111) |
                       |  Cluster NLB         |
                       +---------------+       
                               |
              +--------Répartition de Charge-------+
              |                 |                 |
         Serveur 1          Serveur 2         Serveur 3
         (Win2012ND1)       (Win2012ND2)      (WinCoreND3)
       IP NLB: 192.168.0.11  IP NLB: 192.168.0.22 IP NLB: 192.168.0.33
```


------------------
#  02 - Installation et Configuration d’un Cluster NLB sous Windows Server 2016/2019
------------------------

## Introduction au Cluster NLB (Network Load Balancer)

Un **Network Load Balancer (NLB)** est une fonctionnalité de Windows Server qui permet de répartir les demandes réseau (trafic) entre plusieurs serveurs. Cela permet de :
- **Répartir la charge** pour éviter la surcharge d’un seul serveur.
- **Assurer la redondance** en cas de panne d’un des serveurs, car les autres serveurs peuvent prendre le relais.

### Objectifs du Laboratoire

1. **Installer et configurer** un serveur en mode Core pour la répartition de charge.
2. **Assurer la répartition de charge** en ajoutant un troisième nœud à un cluster NLB existant.
3. **Tester la redondance** en cas de panne de certains serveurs.

### Prérequis

Pour ce laboratoire, vous aurez besoin de :
- **4 machines Windows Server 2016/2019** (dont un contrôleur de domaine et trois serveurs pour le NLB).
- **Une machine cliente Windows 10** (pour tester l’accès au cluster).
- **Hyperviseur** tel que VMware ou VirtualBox.

### Concepts Clés

- **Mode Core** : Une installation minimale de Windows Server sans interface graphique, idéale pour les performances et la sécurité.
- **VIP (Virtual IP)** : Adresse IP virtuelle utilisée par le cluster NLB pour diriger les requêtes vers les serveurs.
- **DNS (Domain Name System)** : Service qui permet d’associer un nom (ex. `ClusterWeb.test.local`) à une adresse IP.

---

## Schéma de Répartition de Charge NLB

```
                             Internet
                                 |
                                 |
                         +---------------+
                         |   Switch       |
                         +---------------+
                                 |
        -------------------------------------------------
        |                |                |             |
  +------------+   +------------+   +------------+    +----------------+
  | Serveur 1  |   | Serveur 2  |   | Serveur 3  |    |  Contrôleur de |
  | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |    |  Domaine       |
  | test.local |   | test.local |   | test.local |    |  (DNS)         |
  | IP: 192.   |   | IP: 192.   |   | IP: 192.   |    | test.local     |
  | 168.0.1    |   | 168.0.2    |   | 168.0.3    |    |                |
  | NLB: 192.  |   | NLB: 192.  |   | NLB: 192.  |    |                |
  | 168.0.11   |   | 168.0.22   |   | 168.0.33   |    |                |
  | Unique ID: |   | Unique ID: |   | Unique ID: |    |                |
  | 1          |   | 2          |   | 3          |    |                |
  +------------+   +------------+   +------------+    +----------------+
                                 |
                    Adresse IP du Cluster:
                        ClusterWeb.test.local
                          IP: 192.168.0.111
```

---

## Étapes du Laboratoire en Détail

### A. Installation et Configuration Initiale de WinCoreND3 (20 pts)

#### 1. Installation et Configuration de `WinCoreND3`

1. **Créer une machine virtuelle** pour `WinCoreND3`.
   - **Nom d’hôte** : `WinCoreND3`
   - **Première interface réseau** : IP = `192.168.0.3 /24` (pour la gestion)
   - **Deuxième interface réseau** : IP = `192.168.0.33 /24` (pour le trafic NLB)

2. **Configurer le nom d’hôte et les adresses IP** :
   - Utilisez la commande `sconfig` pour configurer le nom d’hôte :
     ```powershell
     sconfig
     ```
   - Sélectionnez l’option `2` pour définir le nom d’hôte en `WinCoreND3`.

   - Ensuite, configurez les adresses IP avec PowerShell :
     ```powershell
     # IP pour la gestion
     New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.0.3 -PrefixLength 24

     # IP pour le trafic NLB
     New-NetIPAddress -InterfaceAlias "Ethernet 2" -IPAddress 192.168.0.33 -PrefixLength 24
     ```

#### 2. Joindre `WinCoreND3` au Domaine `test.local`

   ```powershell
   Add-Computer -DomainName "test.local" -Credential (Get-Credential)
   Restart-Computer
   ```

#### 3. Installation du Service IIS

   Installez le rôle IIS et personnalisez la page d'accueil pour inclure votre nom :

   ```powershell
   Install-WindowsFeature -Name Web-Server -IncludeManagementTools
   Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "Bienvenue, [Votre Nom]"
   ```

### B. Installation de la Fonctionnalité NLB (20 pts)

#### 4. Installer les fonctionnalités NLB et RSAT-NLB

   Sur `WinCoreND3`, installez les fonctionnalités pour activer le NLB :

   ```powershell
   Install-WindowsFeature -Name NLB, RSAT-NLB
   ```

#### 5. Vérifier l’installation des fonctionnalités

   Sur le contrôleur de domaine, vérifiez que `WinCoreND3` dispose bien des fonctionnalités NLB :

   ```powershell
   Get-WindowsFeature -ComputerName "WinCoreND3" | Where-Object { $_.Installed -eq $true }
   ```

### C. Gestion du Cluster NLB (10 pts)

#### 6. Créer le Cluster et Ajouter `WinCoreND3` comme Nœud

1. **Créer le cluster NLB** :
   - Depuis `Win2012ND1`, créez le cluster NLB et spécifiez la VIP :
     ```powershell
     New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
     ```

2. **Ajouter `WinCoreND3` au cluster** :
   - Sur `WinCoreND3`, ajoutez le serveur au cluster avec un identifiant unique (Unique ID) de 3 :
     ```powershell
     Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "WinCoreND3.test.local" -UniqueId 3
     ```

3. **Vérifier la configuration des nœuds dans le cluster** :
   ```powershell
   Get-NlbClusterNode -ClusterName "ClusterWeb"
   ```

### D. Test du Cluster NLB (40 pts)

#### 7. Configuration DNS pour l'adresse du cluster

1. **Ajouter des enregistrements DNS** :
   - Depuis le serveur DNS, ajoutez un enregistrement **A** pour `ClusterWeb.test.local` pointant vers `192.168.0.111`, ainsi qu’un **CNAME** pour `www.test.local` pointant vers `ClusterWeb.test.local`.

   Exemple de commandes PowerShell pour ajouter ces enregistrements :
   ```powershell
   Add-DnsServerResourceRecordA -Name "ClusterWeb" -ZoneName "test.local" -IPv4Address "192.168.0.111"
   Add-DnsServerResourceRecordCName -Name "www" -ZoneName "test.local" -HostNameAlias "ClusterWeb.test.local"
   ```

#### 8. Test d'accès depuis le client

- Depuis la machine `WinCLI`, ouvrez un navigateur et accédez à l’URL suivante :
  ```
  http://www.test.local
  ```
- Vérifiez que la page d’accueil d’IIS est affichée.

#### 9. Test de basculement en cas de panne

1. **Simuler une panne de nœuds** en désactivant temporairement `Win2012ND1` et `Win2012ND2` :
   ```powershell
   # Sur Win2012ND1 et Win2012ND2
   Stop-NlbClusterNode -InterfaceName "Ethernet"
   ```
2. **Vérifier la disponibilité** de `WinCoreND3` en accédant à nouveau à `www.test.local` depuis `WinCLI`.

3. **Remettre en ligne les nœuds désactivés** après les tests :
   ```powershell
   Start-NlbClusterNode -InterfaceName "Ethernet"
   ```

#### 10. Script de vérification automatique de la connectivité

Créez un script PowerShell pour tester automatiquement la connectivité de chaque nœud du cluster :

```powershell
# Liste des nœuds du cluster
$nodes = @("Win2012ND1",

 "Win2012ND2", "WinCoreND3")

foreach ($node in $nodes) {
    if (Test-Connection -ComputerName $node -Count 2 -Quiet) {
        Write-Host "$node est en ligne"
    } else {
        Write-Host "$node est hors ligne"
    }
}
```

Ce script permet de vérifier rapidement l'état de chaque serveur.

---

### Conclusion

Ce cours vous a guidé à travers l'installation, la configuration et le test d'un cluster NLB sous Windows Server 2016/2019. Vous avez appris à configurer un serveur en mode Core, à ajouter un nœud au cluster NLB, et à tester la redondance du cluster. En suivant ces étapes, vous êtes bien préparé pour configurer et maintenir un environnement NLB en production.
