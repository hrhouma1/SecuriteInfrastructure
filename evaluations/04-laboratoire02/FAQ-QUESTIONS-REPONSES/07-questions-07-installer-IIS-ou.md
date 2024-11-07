# IIS
------------------------
# Question 1 - La page web est hébérgée au niveau de quel serveur ?
------------------------

Dans un cluster NLB, la page Web est hébergée sur **chacun des serveurs du cluster**. Chaque serveur participant au cluster NLB doit avoir une **configuration identique** pour garantir une expérience utilisateur cohérente, peu importe quel serveur répond à la requête.

### Comment ça fonctionne concrètement ?

1. **Déploiement de la page sur chaque serveur** :
   - Lors de la configuration du cluster NLB, la page Web (par exemple, la page d'accueil IIS personnalisée) doit être **déployée sur chaque serveur** du cluster (Win2012ND1, Win2012ND2, et WinCoreND3).
   - Cela signifie que chaque serveur héberge la même version de la page Web, assurant une redondance et une continuité de service.

2. **Répartition des requêtes par le NLB** :
   - Lorsque les utilisateurs accèdent à l’adresse du cluster (`http://ClusterWeb.test.local` ou `http://192.168.0.111`), le NLB redirige la requête vers l’un des serveurs disponibles.
   - Le serveur qui reçoit la requête renvoie la page Web à l'utilisateur. Cependant, l'utilisateur ne sait pas forcément quel serveur a répondu ; il voit simplement la page Web.

3. **Gestion du basculement** :
   - Si l’un des serveurs tombe en panne, le NLB exclut ce serveur temporairement. Les requêtes sont alors redirigées vers les autres serveurs restants qui hébergent également la page.
   - Grâce à la configuration identique, l’utilisateur continue de voir la même page, peu importe quel serveur répond.

### En résumé

La page Web est **hébergée sur chaque serveur** du cluster NLB. Cela signifie que si l'utilisateur accède à `http://ClusterWeb.test.local`, le NLB décide du serveur qui traitera la requête, mais chaque serveur renvoie exactement la même page, garantissant une expérience uniforme et une haute disponibilité.


------------------------
# Question 2 - Explication comment le cluster NLB gère l’accès à la page Web
------------------------

# Explication comment le cluster NLB gère l’accès à la page Web.

1. **Enregistrement DNS (CNAME) et VIP du Cluster**  
   En effet, nous devons créer un enregistrement **CNAME** dans le DNS qui redirige `www.ClusterWeb.test.local` vers `ClusterWeb.test.local`. Cependant, cet enregistrement DNS ne pointe pas vers un serveur spécifique (comme WinCoreND3) mais vers l'**adresse IP virtuelle (VIP)** du cluster, qui est `192.168.0.111`. 

   Ainsi, lorsque quelqu'un tape `www.ClusterWeb.test.local` dans son navigateur, la requête est envoyée à l'adresse VIP du cluster NLB.

2. **La page Web n'est pas uniquement sur WinCoreND3**  
   Dans un environnement de **répartition de charge NLB**, chaque serveur (nœud) participant au cluster doit héberger la même page Web. Par conséquent, la page et le service Web ne sont pas seulement sur WinCoreND3, mais également sur les autres serveurs du cluster (Win2012ND1 et Win2012ND2 dans notre cas). Cela garantit que peu importe le serveur qui répond à la requête, l'utilisateur verra toujours la même page.

3. **Fonctionnement du NLB pour la Répartition des Requêtes**  
   Le NLB utilise l'adresse IP virtuelle du cluster (VIP) pour diriger les requêtes vers un des serveurs disponibles de manière équilibrée. Lorsque le DNS renvoie l’adresse `192.168.0.111` pour `www.ClusterWeb.test.local`, le NLB prend cette requête et la redirige vers l’un des serveurs actifs (Win2012ND1, Win2012ND2, ou WinCoreND3).

   Cela signifie que les utilisateurs ne savent pas quel serveur leur envoie la page. Tout ce qu’ils voient, c’est que `www.ClusterWeb.test.local` renvoie la page Web. Si un serveur tombe en panne, le NLB redirige automatiquement les requêtes vers les autres serveurs actifs du cluster.

### En résumé

- Le **CNAME** `www.ClusterWeb.test.local` pointe vers `ClusterWeb.test.local`, qui est associé à l'adresse VIP `192.168.0.111`.
- Tous les serveurs du cluster (Win2012ND1, Win2012ND2, et WinCoreND3) hébergent la **même page Web**, assurant une redondance et une haute disponibilité.
- Le NLB répartit les requêtes entrantes entre les serveurs disponibles, garantissant que l’utilisateur accède toujours à la même page, même si un serveur est hors ligne.

L'objectif de ce point est de clarifier le fonctionnement du NLB et l’utilisation du CNAME. 



------------------------
# Question 3 : Comment configurer le cluster NLB avec le service Web IIS ?
------------------------

Pour configurer le cluster NLB avec le service Web IIS et garantir que la page soit disponible via l'adresse VIP, voici les étapes à suivre :

### 1. Installer NLB sur Tous les Serveurs

Le rôle **NLB** doit être installé sur **chacun des serveurs** du cluster pour qu'ils puissent participer à la répartition de charge. Par exemple, si tu as trois serveurs (`Win2012ND1`, `Win2012ND2`, et `WinCoreND3`), installe NLB sur chacun d’eux avec la commande suivante :

```powershell
Install-WindowsFeature -Name NLB
```

### 2. Installer IIS sur Tous les Serveurs

Le service Web **IIS** doit également être installé sur chaque serveur participant au cluster. Cela permet à chacun d'eux de servir la même page Web, garantissant ainsi que les utilisateurs voient toujours le même contenu, peu importe quel serveur traite la requête.

Sur chaque serveur (`Win2012ND1`, `Win2012ND2`, et `WinCoreND3`), installe IIS avec la commande suivante :

```powershell
Install-WindowsFeature -Name Web-Server -IncludeManagementTools
```

### 3. Configurer la Page Web sur Tous les Serveurs

Après avoir installé IIS, crée la **même page Web** sur chacun des serveurs pour assurer la cohérence. Par exemple, modifie le fichier `index.html` par défaut d’IIS pour qu’il affiche le contenu que tu souhaites.

```powershell
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "Bienvenue sur le cluster NLB de [Nom de l'utilisateur]"
```

Cette configuration doit être réalisée sur chaque serveur (`Win2012ND1`, `Win2012ND2`, et `WinCoreND3`) pour qu’ils servent tous la même page.

### 4. Configurer le Cluster NLB avec une VIP

Une fois que NLB et IIS sont installés sur chaque serveur, configure le **cluster NLB** avec une adresse IP virtuelle (VIP) partagée par tous les serveurs. Tu peux initialiser le cluster sur n'importe lequel des serveurs (par exemple, `Win2012ND1`) avec la commande suivante :

```powershell
New-NlbCluster -InterfaceName "Ethernet" -ClusterName "ClusterWeb" -ClusterPrimaryIP "192.168.0.111" -SubnetMask "255.255.255.0"
```

### 5. Ajouter les Autres Serveurs au Cluster

Ajoute les autres serveurs au cluster pour qu'ils participent à la répartition de charge. Par exemple, pour ajouter `Win2012ND2` et `WinCoreND3` :

```powershell
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "Win2012ND2.test.local"
Add-NlbClusterNode -ClusterName "ClusterWeb" -InterfaceName "Ethernet" -HostName "WinCoreND3.test.local"
```

### 6. Configurer le DNS pour le Cluster

Dans le DNS, crée un enregistrement pour le cluster avec la VIP. Par exemple, configure un enregistrement **CNAME** ou **A** qui pointe `www.ClusterWeb.test.local` vers la VIP `192.168.0.111`.

Exemple de commande PowerShell pour un enregistrement DNS A :

```powershell
Add-DnsServerResourceRecordA -Name "ClusterWeb" -ZoneName "test.local" -IPv4Address "192.168.0.111"
```

### Résumé

1. **Installer NLB** sur chaque serveur du cluster (`Win2012ND1`, `Win2012ND2`, et `WinCoreND3`).
2. **Installer IIS** sur chaque serveur.
3. **Créer la même page Web** sur chaque serveur, dans `C:\inetpub\wwwroot\index.html`.
4. **Configurer le cluster NLB** avec une VIP (par exemple, `192.168.0.111`).
5. **Ajouter tous les serveurs au cluster NLB**.
6. **Configurer le DNS** pour que `www.ClusterWeb.test.local` pointe vers `192.168.0.111`.

En suivant ces étapes, les utilisateurs pourront accéder à la page Web via l'adresse `www.ClusterWeb.test.local`, et le NLB redirigera automatiquement les requêtes vers les serveurs disponibles, assurant ainsi la haute disponibilité et une expérience uniforme.
