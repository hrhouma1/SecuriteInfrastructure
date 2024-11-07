# Laboratoire #2 : Installation et Configuration de la Fonction de Répartition de la Charge Réseau (NLB)

## Objectifs
1. Installer et configurer un serveur en mode Core pour la répartition de charge.
2. Assurer la redondance et la répartition de charge avec Network Load Balancer (NLB) en ajoutant un nœud à un cluster existant.

---

## Étapes de Configuration en PowerShell

### A. Installation et Configuration Initiale (20 pts)

1. **Installation de la machine `WinCoreND3` en mode Core**

   Assurez-vous d'avoir configuré une nouvelle machine virtuelle avec :
   - Nom d’hôte : `WinCoreND3`
   - IP de gestion : `192.168.0.3/24`
   - IP NLB : `192.168.0.33/24`

2. **Configurer le nom d'hôte et les interfaces réseau**

   Utilisez `sconfig` pour définir le nom d'hôte à `WinCoreND3`. Ensuite, configurez les interfaces réseau :

   ```powershell
   # Nom d'hôte
   Rename-Computer -NewName "WinCoreND3" -Restart

   # IP pour la gestion
   New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.0.3 -PrefixLength 24

   # IP pour NLB
   New-NetIPAddress -InterfaceAlias "Ethernet 2" -IPAddress 192.168.0.33 -PrefixLength 24
   ```

3. **Joindre le domaine `test.local`**

   ```powershell
   Add-Computer -DomainName "test.local" -Credential (Get-Credential)
   Restart-Computer
   ```

4. **Installer IIS et configurer la page d'accueil**

   Installez le rôle IIS et personnalisez la page d'accueil :

   ```powershell
   Install-WindowsFeature -Name Web-Server -IncludeManagementTools
   Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "Bienvenue, [Votre Nom]"
   ```

### B. Installation des Fonctionnalités NLB (20 pts)

1. **Installer les fonctionnalités NLB et RSAT-NLB**

   Sur `WinCoreND3`, installez les fonctionnalités NLB et RSAT-NLB :

   ```powershell
   Install-WindowsFeature -Name NLB, RSAT-NLB
   ```

2. **Vérifier l'installation depuis le contrôleur de domaine**

   Connectez-vous au contrôleur de domaine et vérifiez que les fonctionnalités NLB sont bien installées sur `WinCoreND3` :

   ```powershell
   Get-WindowsFeature -ComputerName "WinCoreND3" | Where-Object { $_.Installed -eq $true }
   ```

### C. Gestion du Cluster NLB (10 pts)

1. **Créer le cluster NLB**

   Depuis `Win2012ND1`, créez le cluster NLB et spécifiez l'adresse IP virtuelle (VIP) :

   ```powershell
   New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
   ```

2. **Ajouter `WinCoreND3` au cluster**

   Sur `WinCoreND3`, ajoutez ce serveur en tant que troisième nœud du cluster avec l'identifiant unique 3 :

   ```powershell
   Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "WinCoreND3.test.local" -UniqueId 3
   ```

3. **Vérifier les nœuds du cluster**

   Listez les nœuds du cluster pour confirmer l'ajout de `WinCoreND3` :

   ```powershell
   Get-NlbClusterNode -ClusterName "ClusterWeb"
   ```

### D. Test du Cluster NLB (40 pts)

#### 1. Configuration DNS pour l'adresse du cluster

1. **Ajouter les enregistrements DNS**

   Depuis le contrôleur de domaine, ouvrez la console DNS et ajoutez :
   - Un enregistrement de type **A** pour `ClusterWeb.test.local` pointant vers `192.168.0.111`.
   - Un enregistrement **CNAME** pour `www.test.local` pointant vers `ClusterWeb.test.local`.

   Voici comment créer ces enregistrements via PowerShell (sur le serveur DNS) :

   ```powershell
   Add-DnsServerResourceRecordA -Name "ClusterWeb" -ZoneName "test.local" -IPv4Address "192.168.0.111"
   Add-DnsServerResourceRecordCName -Name "www" -ZoneName "test.local" -HostNameAlias "ClusterWeb.test.local"
   ```

#### 2. Vérification de la Répartition de Charge

1. **Test d'accès via le client**

   À partir de `WinCLI`, ouvrez un navigateur et accédez à :

   ```
   http://www.test.local
   ```

   Vérifiez que la page IIS s'affiche correctement.

2. **Simuler une panne de nœuds**

   Désactivez temporairement `Win2012ND1` et `Win2012ND2` pour tester la redondance.

   ```powershell
   # Sur Win2012ND1 et Win2012ND2, exécutez pour simuler un arrêt
   Stop-NlbClusterNode -InterfaceName "Ethernet"
   ```

   Ensuite, testez à nouveau l'accès sur `WinCLI` pour vérifier que `WinCoreND3` prend la charge.

3. **Remettre en ligne les nœuds**

   Une fois les tests de basculement effectués, remettez en ligne les nœuds désactivés :

   ```powershell
   Start-NlbClusterNode -InterfaceName "Ethernet"
   ```

#### 3. Script PowerShell pour tester automatiquement le basculement

Créez un script PowerShell pour tester la connectivité de chaque nœud :

```powershell
# Liste des noms de nœuds dans le cluster
$nodes = @("Win2012ND1", "Win2012ND2", "WinCoreND3")

foreach ($node in $nodes) {
    if (Test-Connection -ComputerName $node -Count 2 -Quiet) {
        Write-Host "$node est en ligne"
    } else {
        Write-Host "$node est hors ligne"
    }
}
```

Ce script vérifiera l'état de chaque nœud et affichera s'il est en ligne ou hors ligne.

---

## Résumé

Vous avez configuré un cluster NLB en mode Core avec un serveur supplémentaire (`WinCoreND3`) pour assurer la répartition de charge. Vous avez également configuré DNS pour l'accès au cluster, testé la redondance et utilisé un script PowerShell pour surveiller les nœuds du cluster.

En suivant ces étapes, vous assurez une configuration NLB complète et résiliente, avec des tests de basculement pour vérifier la haute disponibilité du service.
