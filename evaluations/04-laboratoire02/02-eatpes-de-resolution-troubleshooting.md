# problèmes pour exécuter les cmdlets NLB sur votre serveur Windows ? 
### ==> suggestions pour résoudre ce problème :

1. Assurez-vous que le module NLB est installé et chargé :

```powershell
Import-Module NetworkLoadBalancingClusters
```

2. Si le module n'est pas reconnu, vous devrez peut-être l'installer. Sur un serveur Core, utilisez :

```powershell
Install-WindowsFeature NLB
```

3. Vérifiez que le module est bien installé :

```powershell
Get-Module -ListAvailable NetworkLoadBalancingClusters
```

4. Si le module est présent mais les cmdlets ne sont toujours pas reconnues, essayez de redémarrer la session PowerShell ou le serveur.

5. Sur Windows Server Core, certaines fonctionnalités RSAT comme RSAT-NLB ne sont pas disponibles. Vous devrez peut-être gérer le cluster NLB à partir d'un autre serveur avec interface graphique.

6. Assurez-vous d'exécuter PowerShell en tant qu'administrateur.

7. Vérifiez que le service NLB est en cours d'exécution :

```powershell
Get-Service nlb
```

- Si ces étapes ne résolvent pas le problème, il est possible qu'il y ait un problème avec l'installation de NLB ou de PowerShell sur votre système. 
- Dans ce cas, vous pourriez envisager de réinstaller les fonctionnalités NLB ou même de réinstaller le système d'exploitation si c'est une option.





# Résumé :

1. Installer la fonctionnalité NLB :

```powershell
Install-WindowsFeature NLB -IncludeManagementTools
```

2. Importer le module NLB :

```powershell
Import-Module NetworkLoadBalancingClusters
```

3. Créer un nouveau cluster NLB :

```powershell
New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
```

4. Ajouter un nœud au cluster :

```powershell
Add-NlbClusterNode -InterfaceName "Ethernet" -NewNodeName "WinCoreND3.test.local" -NewNodeInterface "Ethernet"
```

5. Vérifier la configuration du cluster :

```powershell
Get-NlbCluster
```

6. Vérifier les nœuds du cluster :

```powershell
Get-NlbClusterNode
```

7. Démarrer le cluster si nécessaire :

```powershell
Start-NlbCluster
```

8. Pour arrêter un nœud spécifique :

```powershell
Stop-NlbClusterNode -HostName "WinCoreND3"
```

9. Pour redémarrer un nœud :

```powershell
Start-NlbClusterNode -HostName "WinCoreND3"
```

10. Pour supprimer un nœud du cluster :

```powershell
Remove-NlbClusterNode -HostName "WinCoreND3"
```

Assurez-vous d'exécuter ces commandes avec des privilèges d'administrateur. Si vous rencontrez toujours des problèmes, vérifiez que le service NLB est bien en cours d'exécution :

```powershell
Get-Service nlb
```

Si le service n'est pas démarré, vous pouvez le lancer avec :

```powershell
Start-Service nlb
```

Ces commandes devraient vous permettre d'installer et de configurer correctement NLB sur Windows Server 2019[1][2][4].



