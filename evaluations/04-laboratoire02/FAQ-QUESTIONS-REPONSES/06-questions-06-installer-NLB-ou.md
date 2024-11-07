# Question:

- Dois-je installer le rôle **NLB** sur chacun des serveurs (nœuds) ?
  
# Réponse

Oui, dans un **cluster NLB**, vous devez installer le rôle **NLB** sur chacun des serveurs (nœuds) qui feront partie du cluster. Chaque serveur participant au cluster NLB doit être configuré avec le rôle NLB pour pouvoir distribuer le trafic de manière équitable et assurer la redondance en cas de panne de l'un des nœuds.

Voici les points essentiels :

1. **Installation de NLB sur chaque serveur** : 
   - Installez le rôle **NLB** sur les trois serveurs (par exemple, `Win2012ND1`, `Win2012ND2`, et `WinCoreND3`).
   - Chaque serveur dans le cluster doit avoir le rôle NLB pour participer à la répartition de la charge.

2. **Configuration du cluster NLB** :
   - Une fois le rôle NLB installé sur chaque serveur, vous pouvez configurer le cluster en choisissant un des serveurs pour lancer la création du cluster (ceci n’en fait pas un serveur maître, mais il initie simplement le cluster).
   - Par exemple, sur `Win2012ND1`, vous pouvez créer le cluster NLB en définissant l'adresse IP virtuelle (VIP) partagée, puis ajouter les autres serveurs (`Win2012ND2` et `WinCoreND3`) au cluster.

3. **Ajout des serveurs au cluster** :
   - Après avoir créé le cluster sur l’un des serveurs, ajoutez les autres serveurs au cluster pour qu'ils deviennent des nœuds participants.
   - Chaque nœud dans le cluster aura ainsi la configuration NLB et participera activement à la répartition de la charge réseau.

### Exemple d'installation et de configuration

Voici les étapes de commande PowerShell pour installer et configurer NLB sur chaque serveur :

#### Installation du Rôle NLB sur Chaque Serveur

Exécutez cette commande sur **chaque serveur** (Win2012ND1, Win2012ND2, WinCoreND3) :

```powershell
Install-WindowsFeature -Name NLB
```

#### Création du Cluster NLB sur le Serveur Principal (par exemple, `Win2012ND1`)

Sur **Win2012ND1** (ou un autre serveur de votre choix pour initialiser le cluster), créez le cluster NLB avec l'adresse IP virtuelle (VIP) :

```powershell
New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
```

#### Ajout des Autres Nœuds au Cluster NLB

Une fois le cluster créé, ajoutez les autres serveurs (`Win2012ND2` et `WinCoreND3`) en tant que nœuds du cluster.

Exemple pour ajouter `Win2012ND2` :

```powershell
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "Win2012ND2.test.local" -UniqueId 2
```

Exemple pour ajouter `WinCoreND3` :

```powershell
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "WinCoreND3.test.local" -UniqueId 3
```

### Résumé

- **Installez le rôle NLB sur chaque serveur** (Win2012ND1, Win2012ND2, et WinCoreND3).
- **Configurez le cluster NLB** depuis un serveur (par exemple, Win2012ND1) pour initialiser la VIP et le cluster.
- **Ajoutez les autres serveurs au cluster** pour qu'ils participent à la répartition de charge.
  
En configurant chaque serveur avec NLB, vous assurez que tous les serveurs partagent la charge de manière équilibrée et que le cluster peut basculer automatiquement en cas de panne de l'un des nœuds.


**Question 1 : Dois-je installer le rôle NLB sur tous les serveurs ou seulement sur un seul ?**

**Réponse :**  
Le rôle **NLB** (Network Load Balancer) doit être installé sur **chaque serveur** qui participera au cluster. Dans votre configuration, cela signifie que les trois serveurs (`Win2012ND1`, `Win2012ND2`, et `WinCoreND3`) doivent avoir le rôle NLB installé pour pouvoir partager la charge et assurer la redondance. Chaque serveur qui fait partie du cluster participe de manière égale à la répartition de la charge.

Pour installer le rôle NLB sur chaque serveur, exécutez la commande suivante sur **chaque machine** :

```powershell
Install-WindowsFeature -Name NLB
```

---

**Question 2 : Est-ce qu'un serveur spécifique gère le cluster, comme un maître, ou tous les serveurs sont-ils équivalents ?**

**Réponse :**  
Dans un cluster NLB, il n'y a pas de **serveur maître** ou principal. Tous les serveurs participant au cluster sont **équivalents** et partagent la charge de manière équilibrée. Le NLB est conçu pour fonctionner de manière redondante ; chaque serveur est capable de recevoir et de gérer des requêtes réseau. Si un serveur devient inactif, le NLB redirige automatiquement les requêtes vers les autres serveurs disponibles sans nécessiter de nœud principal.

L’un des serveurs doit initier la **création du cluster** en configurant l'adresse IP virtuelle (VIP) partagée. Cependant, cela ne signifie pas que ce serveur devient un maître – c'est simplement le point de départ de la configuration.

---

**Question 3 : Comment puis-je créer le cluster NLB sur un des serveurs, et lequel dois-je choisir pour cette étape ?**

**Réponse :**  
Pour créer le cluster NLB, choisissez l'un des serveurs, par exemple `Win2012ND1`, pour lancer la création initiale. Cette action n'en fait pas un serveur maître ; il s'agit simplement d'initier le cluster. 

Sur le serveur choisi (`Win2012ND1` dans cet exemple), exécutez la commande suivante pour créer le cluster et définir l’adresse IP virtuelle (VIP) que les clients utiliseront pour se connecter au cluster :

```powershell
New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
```

Ici :
- `InterfaceName` est le nom de l'interface réseau utilisée pour le trafic NLB.
- `ClusterName` est le nom du cluster (ici, "ClusterWeb").
- `ClusterPrimaryIP` est l’adresse IP virtuelle (VIP) partagée par tous les serveurs du cluster.

---

**Question 4 : Comment ajouter les autres serveurs au cluster après l'avoir créé ?**

**Réponse :**  
Après avoir créé le cluster sur le premier serveur (`Win2012ND1`), vous pouvez ajouter les autres serveurs (nœuds) au cluster en utilisant la commande `Add-NlbClusterNode`. Cette opération doit être effectuée pour chaque serveur supplémentaire qui participera au cluster.

Par exemple, pour ajouter `Win2012ND2` au cluster, exécutez la commande suivante sur **Win2012ND1** ou sur **Win2012ND2** :

```powershell
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "Win2012ND2.test.local" -UniqueId 2
```

Et pour ajouter `WinCoreND3` :

```powershell
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "WinCoreND3.test.local" -UniqueId 3
```

Chaque serveur est ajouté en tant que nœud avec un identifiant unique (`UniqueId`), qui permet de les différencier dans le cluster.

---

**Question 5 : Quelle adresse IP dois-je configurer dans le DNS pour que les utilisateurs puissent accéder au cluster ?**

**Réponse :**  
Dans un environnement NLB, le cluster est représenté par une **adresse IP virtuelle (VIP)**, qui est partagée par tous les serveurs. Les utilisateurs se connectent à cette VIP, et le NLB se charge de répartir les requêtes entre les serveurs disponibles.

Pour permettre aux utilisateurs d'accéder au cluster via un nom de domaine, ajoutez un enregistrement de type **A** dans le serveur DNS, qui associe le nom du cluster à la VIP du cluster. 

Par exemple, pour associer `ClusterWeb.test.local` à la VIP `192.168.0.111` :

```powershell
Add-DnsServerResourceRecordA -Name "ClusterWeb" -ZoneName "test.local" -IPv4Address "192.168.0.111"
```

Vous pouvez également ajouter un enregistrement de type **CNAME** pour un alias comme `www.test.local` qui pointe vers `ClusterWeb.test.local` :

```powershell
Add-DnsServerResourceRecordCName -Name "www" -ZoneName "test.local" -HostNameAlias "ClusterWeb.test.local"
```

Ainsi, les utilisateurs peuvent se connecter via `www.test.local`, et le DNS redirigera leur requête vers `192.168.0.111`.

---

**Question 6 : Que se passe-t-il si l'un des serveurs du cluster tombe en panne ?**

**Réponse :**  
Le NLB est conçu pour assurer la **redondance**. Si l'un des serveurs du cluster tombe en panne, le NLB redirigera automatiquement le trafic vers les autres serveurs actifs dans le cluster. Cette capacité de basculement garantit que le service reste disponible pour les utilisateurs même si un serveur devient indisponible.

Le NLB surveille en permanence l'état de chaque nœud dans le cluster. Lorsqu'un serveur ne répond plus aux requêtes, il est temporairement exclu jusqu'à ce qu'il soit de nouveau en ligne.

---

**Question 7 : Pourquoi chaque serveur a-t-il besoin de deux cartes réseau ?**

**Réponse :**  
Chaque serveur du cluster a besoin de deux cartes réseau pour séparer la **gestion** de l'administration du serveur du **trafic NLB**.

1. **Carte de gestion** : Utilisée pour l'administration et la gestion du serveur. Elle a une adresse IP unique, permettant aux administrateurs de se connecter au serveur indépendamment du NLB. Par exemple :
   - Serveur 1 (Win2012ND1) : IP de gestion `192.168.0.1`
   - Serveur 2 (Win2012ND2) : IP de gestion `192.168.0.2`
   - Serveur 3 (WinCoreND3) : IP de gestion `192.168.0.3`

2. **Carte NLB** : Utilisée pour le trafic NLB, avec une adresse IP spécifique pour le NLB, afin de traiter le trafic entrant destiné au cluster. Par exemple :
   - Serveur 1 (Win2012ND1) : IP NLB `192.168.0.11`
   - Serveur 2 (Win2012ND2) : IP NLB `192.168.0.22`
   - Serveur 3 (WinCoreND3) : IP NLB `192.168.0.33`

Cette séparation permet de maintenir un trafic de gestion sécurisé et indépendant du trafic NLB.

---

**Question 8 : Comment puis-je vérifier que le cluster NLB est opérationnel ?**

**Réponse :**  
Pour vérifier l'état des nœuds dans le cluster NLB, vous pouvez utiliser PowerShell. La commande suivante affiche la liste des nœuds et leur état :

```powershell
Get-NlbClusterNode -ClusterName "ClusterWeb"
```

Cela vous indiquera si chaque serveur est en ligne et opérationnel dans le cluster. En outre, vous pouvez tester la disponibilité du cluster en accédant à l'adresse VIP (`192.168.0.111`) depuis un navigateur sur une machine cliente et vérifier que la page d’accueil (ex : IIS) s'affiche correctement.


