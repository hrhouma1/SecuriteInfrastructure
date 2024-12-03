# Annexe 1

# **1. NLB :**

Le **Network Load Balancer (NLB)** que nous configurons dans cet examen est **local**, c'est-à-dire qu'il est uniquement destiné à gérer et répartir les requêtes entre les deux serveurs (SRV1 et SRV2) dans le cadre de notre réseau virtuel **VMnet2**.  
Cela signifie que **vous n'avez pas besoin d'accès à Internet ou d'une passerelle (routeur)** pour que le NLB fonctionne. Les principales vérifications à effectuer sont les suivantes :  

- **Les serveurs SRV1 et SRV2 communiquent-ils bien sur VMnet2 ?**
  - Faites un `ping` entre SRV1 et SRV2 sur leurs adresses IP de VMnet2 (`192.168.2.10` et `192.168.2.20`).
  - Assurez-vous que les adresses sont correctement configurées et qu'elles appartiennent au même réseau (VMnet2).

- **La VIP (Virtual IP) est-elle correctement configurée sur le cluster ?**
  - Vérifiez que la VIP (`192.168.2.100`) est bien active dans votre configuration NLB.
  - Utilisez la commande PowerShell suivante pour valider la configuration :
    ```powershell
    Get-NlbClusterNode
    ```

- **Les interfaces réseau sont-elles bien attribuées ?**
  - Chaque serveur (SRV1 et SRV2) doit avoir deux interfaces réseau :
    - Une sur VMnet1 (gestion et domaine).
    - Une sur VMnet2 (trafic NLB).

---

# **2. Accès à Internet :**

Pour cet examen, **l'accès à Internet n'est pas nécessaire**. Notre configuration est conçue pour fonctionner intégralement dans un environnement local (intranet) simulé grâce aux deux réseaux virtuels **VMnet1** et **VMnet2**. Voici pourquoi :

- **VMnet1 (Gestion & Domaine)** :
  - Utilisé pour connecter toutes les machines au contrôleur de domaine (DC) et au réseau de gestion.
  - Ce réseau permet à vos machines de rejoindre le domaine `exam.local` et d'utiliser les services DNS fournis par le DC.

- **VMnet2 (Trafic NLB)** :
  - Exclusivement utilisé pour le trafic interne entre SRV1, SRV2, et la VIP.

L'absence d'Internet dans cette configuration n'est pas une erreur. Elle est intentionnelle car elle vous pousse à comprendre et à configurer correctement les réseaux locaux. **Vous n'avez donc pas besoin de routeur ou de passerelle (comme pfSense) pour réussir cet examen.**

---

# **3. Exemple de script PowerShell complet pour installer et configurer les outils NLB :**

Je vous propose un script PowerShell complet pour installer et configurer les outils NLB (Network Load Balancing) sur une machine Windows Server vierge, en tenant compte de l'installation des outils RSAT nécessaires. Ce script est divisé en étapes claires pour une exécution simple.

## **1. Installation des Outils NLB et RSAT**
Exécutez ce script pour installer les fonctionnalités nécessaires, y compris NLB et RSAT.

```powershell
# Activer les fonctionnalités NLB et RSAT
Install-WindowsFeature -Name NLB, RSAT-NLB -IncludeAllSubFeature -IncludeManagementTools

# Vérification de l'installation
Get-WindowsFeature | Where-Object { $_.Name -like "NLB*" }
```

## **2. Configuration Réseau**
Avant de configurer NLB, vous devez préparer les interfaces réseau.

```powershell
# Configurer l'adresse IP statique sur la première interface réseau (Ethernet0)
New-NetIPAddress -IPAddress 192.168.1.10 -PrefixLength 24 -DefaultGateway 192.168.1.1 -InterfaceAlias "Ethernet0"

# Configurer l'adresse IP statique sur la deuxième interface réseau (Ethernet1)
New-NetIPAddress -IPAddress 192.168.2.10 -PrefixLength 24 -InterfaceAlias "Ethernet1"

# Configurer le DNS pour le réseau Ethernet0
Set-DnsClientServerAddress -InterfaceAlias "Ethernet0" -ServerAddresses 192.168.1.5

# Vérification des configurations réseau
Get-NetIPAddress | Format-Table
```

## **3. Création du Cluster NLB**
Utilisez ce script pour créer le cluster et ajouter la VIP (Virtual IP).

```powershell
# Importer le module NLB
Import-Module NetworkLoadBalancingClusters

# Créer le cluster sur l'interface Ethernet1
New-NlbCluster -InterfaceName "Ethernet1" -ClusterPrimaryIP 192.168.2.100 -SubnetMask 255.255.255.0

# Ajouter une règle pour gérer le trafic HTTP (port 80)
Add-NlbClusterPortRule -ClusterName "NLBCluster" -StartPort 80 -EndPort 80 -Protocol Both -Affinity Single
```

## **4. Ajouter un Nœud au Cluster**
Si vous configurez plusieurs serveurs, ajoutez-les au cluster.

```powershell
# Ajouter un nœud (SRV2) au cluster existant
Add-NlbClusterNode -HostName "SRV2" -InterfaceName "Ethernet1"
```

## **5. Vérification du Cluster**
Après la configuration, vérifiez que tout fonctionne correctement.

```powershell
# Vérifier l'état du cluster
Get-NlbCluster

# Vérifier l'état des nœuds dans le cluster
Get-NlbClusterNode

# Vérifier les règles du cluster
Get-NlbClusterPortRule
```

## **6. Tests de Connectivité**
1. **Ping la VIP** :
   Depuis une autre machine (CLIENT), exécutez :
   ```powershell
   ping 192.168.2.100
   ```

2. **Tester via un navigateur** :
   Ouvrez `http://192.168.2.100` et vérifiez que les pages IIS des serveurs apparaissent.

## **7. Automatisation Supplémentaire (Optionnel)**
Si vous souhaitez automatiser tout le processus sur plusieurs machines, utilisez ce script sur chaque serveur.

```powershell
# Automatiser la configuration NLB sur plusieurs serveurs
$Servers = @("SRV1", "SRV2")
$ClusterIP = "192.168.2.100"

foreach ($Server in $Servers) {
    Invoke-Command -ComputerName $Server -ScriptBlock {
        Install-WindowsFeature -Name NLB, RSAT-NLB -IncludeAllSubFeature -IncludeManagementTools
        New-NlbCluster -InterfaceName "Ethernet1" -ClusterPrimaryIP $using:ClusterIP -SubnetMask 255.255.255.0
        Add-NlbClusterPortRule -ClusterName "NLBCluster" -StartPort 80 -EndPort 80 -Protocol Both -Affinity Single
    }
}
```

## **Notes Importantes :**
- **Réseau** :
  Assurez-vous que les serveurs sont connectés sur le même réseau pour Ethernet1.
  
- **DNS et DHCP** :
  Les adresses IP doivent être statiques ; évitez d'utiliser DHCP pour les interfaces configurées avec NLB.

- **IIS (Internet Information Services)** :
  Installez IIS sur chaque serveur et configurez une page par défaut pour tester.

## **Vérification Finale**
Une fois le cluster configuré :
- Redémarrez un des serveurs pour tester le basculement.
- Vérifiez que la VIP reste accessible.

Ce script est prêt à l'emploi et couvre tout le nécessaire pour configurer un cluster NLB à partir de zéro.













---------

# **Message clé :**

**L'objectif de cet examen est de maîtriser la gestion et la répartition du trafic dans un réseau local.** Ce que nous évaluons, c'est votre capacité à :
- Configurer un domaine Active Directory (AD DS) local.
- Répartir les requêtes entre deux serveurs via un cluster NLB.
- Tester et analyser les communications réseau entre les machines.

En d'autres termes, si vous configurez vos réseaux locaux (VMnet1 et VMnet2) correctement, tout fonctionnera sans nécessiter d'accès Internet ou d'autres équipements.

---

### **Conseils pour résoudre vos problèmes :**

1. **Pour le NLB :**
   - Relisez votre configuration. Vérifiez les adresses IP, les interfaces réseau, et assurez-vous que la VIP est correctement configurée.
   - Testez la connectivité sur VMnet2 (entre SRV1 et SRV2).
   - Si le NLB ne fonctionne pas, reprenez les étapes d'installation et utilisez les commandes PowerShell pour valider la configuration.

2. **Pour l'accès à Internet :**
   - Ignorez ce point pour cet examen. L'accès à Internet n'est pas nécessaire, et sa mise en place ne fait pas partie des objectifs de cet exercice.

---

### **Rappel :**
- Je mise sur votre capacité à produire une configuration **claire et fonctionnelle** dans un environnement local. 
- Si vous respectez les étapes et la logique de l'énoncé, vous n'aurez pas de problème. 
- Prenez le temps de tester méthodiquement chaque composant avant de passer à l'étape suivante.

Courage à tous, et n'hésitez pas à poser vos questions si besoin !

