**Question** :  
*Dans le cadre du laboratoire sur la configuration d’un cluster NLB, quel nœud est responsable de la gestion des autres serveurs ? Est-ce qu’il y a un serveur principal qui gère le basculement, et comment le DNS et les cartes réseau sont-ils configurés pour assurer une répartition de charge efficace ?*

---

**Réponse :**

Dans un cluster NLB (Network Load Balancer), il n'y a pas de "nœud principal" qui gère les autres serveurs comme dans certains types de clusters à haute disponibilité. Voici comment la gestion des nœuds et la répartition de charge sont assurées :

1. **Fonctionnement du NLB sans nœud principal** :
   - Tous les serveurs du cluster NLB (appelés nœuds) fonctionnent de manière **équivalente et redondante**.
   - Il n'y a pas de serveur principal responsable de la gestion des autres. Au lieu de cela, chaque nœud du cluster participe à la répartition de la charge de manière **égalitaire**.
   - Lorsque le NLB est configuré, il distribue les requêtes entrantes parmi les nœuds disponibles selon une stratégie de répartition (comme le Round Robin).
   - En cas de panne d'un nœud, les autres serveurs prennent automatiquement le relais pour assurer la continuité du service.

2. **Adresse IP virtuelle (VIP) du Cluster** :
   - Le NLB utilise une **adresse IP virtuelle (VIP)** pour représenter le cluster en tant qu'entité unique. Dans ce laboratoire, la VIP est **192.168.0.111**.
   - Les clients se connectent à cette VIP, et le NLB redirige les requêtes vers l'un des serveurs disponibles.
   - Aucun des nœuds ne "gère" les autres ; ils fonctionnent en **parallèle** pour répondre aux requêtes via cette VIP.

3. **Configuration DNS pour le Cluster** :
   - Dans le DNS, créez un enregistrement de type **A** pour associer le nom de domaine du cluster (`ClusterWeb.test.local`) à la VIP **192.168.0.111**.
   - Cela permet aux utilisateurs et aux applications de se connecter au cluster via le nom `ClusterWeb.test.local`, qui est plus facile à retenir.
   - Vous pouvez également ajouter un enregistrement **CNAME** pour `www.test.local` pointant vers `ClusterWeb.test.local`, pour offrir une autre option de connexion.

   Exemple de configuration DNS :
   ```powershell
   Add-DnsServerResourceRecordA -Name "ClusterWeb" -ZoneName "test.local" -IPv4Address "192.168.0.111"
   Add-DnsServerResourceRecordCName -Name "www" -ZoneName "test.local" -HostNameAlias "ClusterWeb.test.local"
   ```

4. **Configuration des deux cartes réseau par serveur** :
   - Chaque nœud du cluster doit avoir deux cartes réseau configurées pour gérer le trafic NLB et la gestion de l’administration.
     - **Carte 1 (IP de gestion)** : Utilisée pour administrer chaque serveur individuellement avec une adresse IP unique, par exemple :
       - Serveur 1 : IP de gestion `192.168.0.1`
       - Serveur 2 : IP de gestion `192.168.0.2`
       - Serveur 3 : IP de gestion `192.168.0.3`
     - **Carte 2 (IP NLB)** : Utilisée pour le trafic NLB avec une adresse IP dédiée pour chaque serveur dans le cluster, par exemple :
       - Serveur 1 : IP NLB `192.168.0.11`
       - Serveur 2 : IP NLB `192.168.0.22`
       - Serveur 3 : IP NLB `192.168.0.33`

5. **Basculement automatique sans nœud principal** :
   - Le NLB prend en charge le basculement de manière automatique. Si un nœud du cluster devient inactif ou tombe en panne, le NLB redirige automatiquement les requêtes vers les nœuds restants.
   - Ce fonctionnement assure une **haute disponibilité** sans intervention humaine pour désigner un nouveau nœud "maître".

### Schéma Résumé du Cluster et du Workflow

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

### Résumé de la configuration

- **Pour le DNS** : Ajoutez un enregistrement de type **A** dans le DNS pour `ClusterWeb.test.local`, pointant vers **192.168.0.111** (VIP du cluster).
- **Accès client** : Les clients utilisent l’adresse **192.168.0.111** ou `ClusterWeb.test.local` pour se connecter. Le NLB s’occupe de la répartition de charge entre les serveurs disponibles du cluster.
  
Cette configuration assure que les demandes sont réparties efficacement entre les serveurs et que le service reste disponible même si l'un des nœuds tombe en panne.
