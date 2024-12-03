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

## **3.1. Installation des Outils NLB et RSAT**
Exécutez ce script pour installer les fonctionnalités nécessaires, y compris NLB et RSAT.

```powershell
# Activer les fonctionnalités NLB et RSAT
Install-WindowsFeature -Name NLB, RSAT-NLB -IncludeAllSubFeature -IncludeManagementTools

# Vérification de l'installation
Get-WindowsFeature | Where-Object { $_.Name -like "NLB*" }
```

## **3.2. Configuration Réseau**
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

## **3.3. Création du Cluster NLB**
Utilisez ce script pour créer le cluster et ajouter la VIP (Virtual IP).

```powershell
# Importer le module NLB
Import-Module NetworkLoadBalancingClusters

# Créer le cluster sur l'interface Ethernet1
New-NlbCluster -InterfaceName "Ethernet1" -ClusterPrimaryIP 192.168.2.100 -SubnetMask 255.255.255.0

# Ajouter une règle pour gérer le trafic HTTP (port 80)
Add-NlbClusterPortRule -ClusterName "NLBCluster" -StartPort 80 -EndPort 80 -Protocol Both -Affinity Single
```

## **3.4. Ajouter un Nœud au Cluster**
Si vous configurez plusieurs serveurs, ajoutez-les au cluster.

```powershell
# Ajouter un nœud (SRV2) au cluster existant
Add-NlbClusterNode -HostName "SRV2" -InterfaceName "Ethernet1"
```

## **3.5. Vérification du Cluster**
Après la configuration, vérifiez que tout fonctionne correctement.

```powershell
# Vérifier l'état du cluster
Get-NlbCluster

# Vérifier l'état des nœuds dans le cluster
Get-NlbClusterNode

# Vérifier les règles du cluster
Get-NlbClusterPortRule
```

## **3.6. Tests de Connectivité**
1. **Ping la VIP** :
   Depuis une autre machine (CLIENT), exécutez :
   ```powershell
   ping 192.168.2.100
   ```

2. **Tester via un navigateur** :
   Ouvrez `http://192.168.2.100` et vérifiez que les pages IIS des serveurs apparaissent.

## **3.7. Automatisation Supplémentaire (Optionnel)**
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

# 4. Routage ? - Communication entre VMnet1 et VMnet2 ?


### **Question :**

- Dans l’architecture donnée, le **client connecté à VMnet1** doit accéder à la **Virtual IP (VIP)** située sur **VMnet2**. 
- Cela est en effet impossible sans une configuration spécifique permettant la communication entre ces deux réseaux distincts.

---

### **Explication **

Les réseaux **VMnet1** et **VMnet2** représentent deux segments distincts. Par défaut, une machine dans **VMnet1** (par exemple, le CLIENT) **ne peut pas communiquer avec les machines ou la VIP dans VMnet2**, car ils n’appartiennent pas au même réseau logique. 

**Pourquoi ?**
- Les **réseaux locaux (VMnets)** fonctionnent comme des isolations virtuelles. Les machines sur VMnet1 ne connaissent pas l’existence de VMnet2 et vice versa.
- Sans un appareil ou une configuration qui agit comme un **routeur**, les paquets réseau ne peuvent pas être transférés d’un réseau à l’autre.

---

### **Solutions : Comment permettre la communication entre VMnet1 et VMnet2 ?**

Vous avez deux options principales pour résoudre ce problème, selon le niveau de complexité et vos objectifs.

---

#### **Solution 1 : Utilisation d’un Serveur Windows comme Routeur**
Un serveur Windows peut être configuré pour jouer le rôle de **routeur** entre les réseaux VMnet1 et VMnet2. Voici comment procéder :

1. **Configuration Réseau sur le Serveur Routeur :**
   - Sélectionnez une machine connectée à **VMnet1** et **VMnet2** (par exemple, SRV1 ou un serveur dédié).
   - Assurez-vous que cette machine a deux interfaces réseau :
     - Une sur **VMnet1** (exemple : IP 192.168.1.254).
     - Une sur **VMnet2** (exemple : IP 192.168.2.254).

2. **Activer le Routage sur le Serveur :**
   Exécutez ces commandes PowerShell sur la machine choisie comme routeur :
   ```powershell
   # Activer le service de routage
   Install-WindowsFeature -Name RemoteAccess -IncludeManagementTools

   # Configurer le routage LAN-LAN
   Install-WindowsFeature -Name RSAT-RemoteAccess

   # Activer l’IP Forwarding
   Set-NetIPInterface -InterfaceAlias "Ethernet0" -Forwarding Enabled
   Set-NetIPInterface -InterfaceAlias "Ethernet1" -Forwarding Enabled
   ```

3. **Configurer les Routes sur les Machines Client :**
   Ajoutez une route statique sur le CLIENT pour diriger le trafic destiné à VMnet2 via le routeur :
   ```powershell
   route add 192.168.2.0 mask 255.255.255.0 192.168.1.254
   ```

---

#### **Solution 2 : Utilisation de pfSense comme Routeur**

pfSense est un pare-feu et routeur open-source facile à configurer. Voici les étapes pour utiliser pfSense comme routeur entre VMnet1 et VMnet2.

1. **Installer pfSense :**
   - Téléchargez l’ISO de pfSense et créez une machine virtuelle dédiée.
   - Configurez deux interfaces réseau pour pfSense :
     - Interface **WAN** connectée à **VMnet1**.
     - Interface **LAN** connectée à **VMnet2**.

2. **Configurer les Interfaces dans pfSense :**
   - Accédez à l’interface web de pfSense (par défaut : `192.168.1.1` sur WAN).
   - Configurez une passerelle entre WAN (VMnet1) et LAN (VMnet2).
   - Exemple :
     - WAN (VMnet1) : IP 192.168.1.254
     - LAN (VMnet2) : IP 192.168.2.254

3. **Configurer les Règles de Routage :**
   - Ajoutez une règle autorisant le trafic entre les deux réseaux dans pfSense :
     - Source : 192.168.1.0/24
     - Destination : 192.168.2.0/24

4. **Configurer le CLIENT :**
   - Ajoutez une route sur le CLIENT ou configurez son **passerelle par défaut** pour utiliser pfSense comme routeur :
     ```powershell
     route add 192.168.2.0 mask 255.255.255.0 192.168.1.254
     ```

---

### **Accès à Internet**

Si vous souhaitez que les machines dans VMnet1 et VMnet2 accèdent à Internet, voici les étapes :

1. **Configurer une Interface WAN vers l’Internet :**
   - Ajoutez une troisième interface à pfSense ou au serveur routeur, configurée pour accéder à votre connexion Internet.
   - Exemple :
     - Interface WAN : DHCP ou IP publique.

2. **Configurer le NAT (Network Address Translation) :**
   - Dans pfSense, activez le NAT pour permettre aux machines locales (VMnet1 et VMnet2) d’accéder à Internet via l’interface WAN.

3. **Configurer les Passerelles :**
   - Configurez chaque machine pour utiliser le routeur (pfSense ou Windows) comme passerelle par défaut.

---

### **Quelle Solution Choisir ?**
1. **Simplicité et Familiarité :** Utilisez un serveur Windows comme routeur si vous préférez rester dans un environnement Windows.
2. **Performance et Flexibilité :** Utilisez pfSense pour une solution professionnelle, évolutive et mieux adaptée aux scénarios avancés (NAT, filtrage, etc.).

---

# **Conclusion**
Il est **essentiel** de comprendre que sans une configuration de routage explicite, **VMnet1 et VMnet2 ne peuvent pas communiquer**. Ces solutions permettent de résoudre ce problème tout en respectant les objectifs pédagogiques du laboratoire.





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

