# Install-WindowsFeature -Name NLB, RSAT-NLB not found


# **Question :**  
*Je rencontre des difficultés pour installer les outils RSAT-NLB sur Windows Server Core. La commande `Install-WindowsFeature -Name NLB, RSAT-NLB` retourne une erreur indiquant que RSAT n'est pas disponible. Comment puis-je gérer NLB sur Windows Server Core sans les outils RSAT ?*

---

# **Réponse :**

Sur **Windows Server Core**, certains outils **RSAT (Remote Server Administration Tools)**, y compris **RSAT-NLB**, ne sont pas disponibles car ils sont conçus pour fonctionner principalement sur une machine avec interface graphique (GUI). 

### Solution pour Installer et Gérer NLB sur Windows Server Core

1. **Installer uniquement le rôle NLB sur Windows Server Core**  
   Sur Windows Server Core, installez uniquement le rôle **NLB** sans les outils RSAT, car l’administration du cluster peut se faire en ligne de commande ou à distance depuis une autre machine avec GUI.

   Utilisez la commande suivante pour installer NLB :

   ```powershell
   Install-WindowsFeature -Name NLB
   ```

   Cette commande installe le service NLB uniquement, sans les outils d’administration RSAT-NLB.

2. **Utiliser une Machine Distante avec GUI pour Gérer le Cluster NLB**  
   Pour administrer NLB avec une interface graphique, utilisez une autre machine Windows, par exemple :
   - Un autre serveur Windows avec GUI (par exemple, Windows Server 2016/2019 Standard ou Datacenter).
   - Un PC Windows 10/11 où vous pouvez ajouter les outils RSAT.

   **Étapes pour configurer la machine distante** :
   - Sur cette machine avec GUI, installez **RSAT-NLB** :
     - Sur un serveur Windows avec GUI, utilisez le Gestionnaire de serveur pour ajouter la fonctionnalité.
     - Sur Windows 10/11, allez dans **Paramètres** > **Applications** > **Fonctionnalités facultatives** > **Ajouter une fonctionnalité**, puis ajoutez **RSAT-NLB**.

   - Une fois RSAT installé, ouvrez l'outil **Network Load Balancing Manager** et connectez-vous au serveur Core où NLB est installé pour administrer le cluster.

3. **Administrer le Cluster NLB directement depuis le Serveur Core avec PowerShell**  
   Si vous ne disposez pas d'une machine distante avec GUI, vous pouvez également configurer et gérer le cluster NLB directement sur le serveur Core via PowerShell. Voici quelques commandes utiles :

   - **Créer un nouveau cluster NLB** :
     ```powershell
     New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
     ```

   - **Ajouter un nœud au cluster NLB** :
     ```powershell
     Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "NomDuServeurCore" -UniqueId 3
     ```

   - **Vérifier les nœuds du cluster** :
     ```powershell
     Get-NlbClusterNode -ClusterName "ClusterWeb"
     ```

### Résumé

- Sur Windows Server Core, installez uniquement le rôle **NLB** avec `Install-WindowsFeature -Name NLB`.
- Utilisez une machine distante avec GUI pour installer **RSAT-NLB** et gérer le cluster NLB via une interface graphique.
- Utilisez **PowerShell** pour administrer NLB directement sur Windows Server Core si une machine avec GUI n'est pas disponible.

En suivant ces étapes, vous pourrez configurer et administrer le cluster NLB sur un serveur Core même sans interface graphique ni outils RSAT installés localement.


-------------------
# Annexe : L'installation des outils **RSAT (Remote Server Administration Tools)** sur **Windows Server Core** peut poser des problèmes  ?
-------------------


- L'installation des outils **RSAT (Remote Server Administration Tools)** sur **Windows Server Core** peut poser des problèmes car tous les outils ne sont pas disponibles ou installables dans cette version. Les fonctionnalités **RSAT** sont souvent conçues pour être utilisées depuis une **machine avec une interface graphique** ou à distance sur un **serveur avec GUI** (Graphical User Interface). Voici comment contourner cette limitation.

### 1. Installer uniquement le Rôle NLB sur Windows Server Core

Sur Windows Server Core, vous pouvez installer **NLB** sans **RSAT-NLB**, car RSAT est principalement utilisé pour administrer NLB via une interface graphique. Pour un serveur Core, l’administration de NLB se fera uniquement en ligne de commande.

Utilisez la commande suivante pour installer uniquement **NLB** sur Windows Server Core :

```powershell
Install-WindowsFeature -Name NLB
```

Cette commande devrait fonctionner et installer uniquement le service NLB, sans les outils RSAT-NLB.

### 2. Administrer NLB depuis une Machine Distante (avec Interface Graphique)

Pour gérer le cluster NLB depuis une interface graphique, vous pouvez utiliser une autre machine Windows (par exemple, un autre serveur Windows avec GUI ou même une machine Windows 10) et y installer les **outils RSAT-NLB**.

#### Étapes pour configurer une machine distante pour administrer le cluster NLB

1. **Installer RSAT sur une machine avec interface graphique** :
   - Sur une machine Windows Server avec GUI ou un PC Windows 10, installez les outils **RSAT-NLB**. 
   - Sur un PC Windows 10, vous pouvez installer RSAT via les fonctionnalités facultatives dans **Paramètres** > **Applications** > **Fonctionnalités facultatives** > **Ajouter une fonctionnalité**.

2. **Connecter à Windows Server Core pour administrer NLB** :
   - Une fois RSAT installé, ouvrez le gestionnaire NLB sur votre machine distante avec interface graphique.
   - Connectez-vous à votre serveur Core où NLB est installé. Vous pourrez alors gérer le cluster depuis cette machine.

### 3. Administration NLB via PowerShell sur Server Core

Si vous devez configurer et gérer NLB directement depuis le serveur Core (sans interface graphique), vous pouvez utiliser PowerShell. Voici quelques commandes utiles pour gérer un cluster NLB :

- **Créer un nouveau cluster NLB** :

   ```powershell
   New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
   ```

- **Ajouter un nœud au cluster NLB** :

   ```powershell
   Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "NomDuServeurCore" -UniqueId 3
   ```

- **Vérifier les nœuds du cluster** :

   ```powershell
   Get-NlbClusterNode -ClusterName "ClusterWeb"
   ```

### Résumé

- Installez uniquement le rôle **NLB** sur Windows Server Core avec `Install-WindowsFeature -Name NLB`.
- Utilisez une **machine distante avec GUI** pour installer **RSAT-NLB** et gérer le cluster NLB via l'interface graphique.
- Utilisez **PowerShell** pour administrer NLB sur le serveur Core si une machine avec interface graphique n'est pas disponible.

En suivant ces étapes, vous pourrez configurer et administrer le cluster NLB même sans interface graphique sur Windows Server Core.
