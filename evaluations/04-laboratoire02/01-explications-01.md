# Document explicatif : Cluster NLB sous Windows Server 2016/2019

**Titre du Laboratoire** : Installation et Configuration de la Fonction de Répartition de la Charge Réseau (NLB)  

**Objectifs du Laboratoire** :
1. Installation et configuration de Windows Server 2016/2019 Core.
2. Assurer la répartition de charge réseau en ajoutant un nœud (Windows Server 2016/2019 Core) à un cluster existant.



---

# 1. Présentation du Cluster NLB

Un **Network Load Balancer (NLB)** est une fonctionnalité qui permet de répartir la charge réseau entre plusieurs serveurs. L'objectif est d'augmenter la disponibilité et la performance des services, tout en assurant la redondance en cas de panne de l'un des serveurs.

Dans ce laboratoire, nous allons ajouter un troisième nœud à un cluster existant pour assurer une meilleure répartition de la charge. Le cluster NLB est configuré pour que chaque serveur partage la charge des requêtes entrantes de manière équilibrée.

# 2. Composants du Cluster

Le cluster NLB comprend les éléments suivants :

- **Trois serveurs Windows Server 2016/2019 Core** :
  - Win2012ND1.test.local
  - Win2012ND2.test.local
  - WinCoreND3.test.local (nouveau nœud ajouté dans ce laboratoire)
  
- **Un contrôleur de domaine Windows Server 2016/2019** :
  - Gère le domaine `test.local` et assure la gestion DNS pour l'accès au cluster via des noms conviviaux comme `ClusterWeb.test.local`.

- **Une machine cliente Windows 10 Professionnel (WinCLI)** :
  - Utilisée pour tester l'accès au cluster via le réseau et vérifier la répartition de charge.

---

# 3. Détails de Configuration

Chaque serveur du cluster possède :
- Une **IP de gestion** pour l’administration et la configuration.
- Une **IP dédiée à la répartition de charge (NLB)** pour gérer le trafic utilisateur.

Voici la configuration IP des serveurs :

| Serveur              | Nom d'Hôte               | IP de Gestion    | IP NLB          | Unique ID |
|----------------------|--------------------------|------------------|-----------------|-----------|
| Serveur 1            | Win2012ND1.test.local    | 192.168.0.1      | 192.168.0.11    | 1         |
| Serveur 2            | Win2012ND2.test.local    | 192.168.0.2      | 192.168.0.22    | 2         |
| Serveur 3            | WinCoreND3.test.local    | 192.168.0.3      | 192.168.0.33    | 3         |

Le **cluster** a une adresse IP virtuelle partagée (VIP) : **192.168.0.111**. C’est cette adresse IP que les utilisateurs utilisent pour accéder au service, et le NLB se charge de répartir les demandes entre les serveurs.

---

# 4. Fonctionnement de la Répartition de Charge (NLB)

Le NLB utilise les adresses IP NLB pour gérer le trafic utilisateur de manière équilibrée entre les serveurs. Si l'un des serveurs tombe en panne ou est surchargé, le NLB redirige automatiquement les demandes vers les autres serveurs disponibles.

---

# 5. Schéma ASCII du Cluster NLB

Voici une représentation ASCII du cluster NLB :

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

# 6. Étapes du Laboratoire

### A. Installation et Configuration de Base
1. Installez et configurez le serveur `WinCoreND3` avec une **IP de gestion** (`192.168.0.3`) et une **IP NLB** (`192.168.0.33`).
2. Joignez le serveur au domaine `test.local`.
3. Installez le service IIS et configurez la page d’accueil avec votre nom.

### B. Installation des Fonctionnalités NLB
4. Utilisez PowerShell pour installer les fonctionnalités NLB et RSAT-NLB sur `WinCoreND3`.
5. Vérifiez que les fonctionnalités sont bien installées à partir du contrôleur de domaine.

### C. Gestion du Cluster NLB
6. Ajoutez le serveur `WinCoreND3` au cluster existant `ClusterWeb.test.local` avec l'identifiant unique **3**.

### D. Test du Cluster NLB
7. Dans le serveur DNS, ajoutez un enregistrement A pour `ClusterWeb.test.local` et un CNAME `www` pointant vers `ClusterWeb.test.local`.
8. À partir de la machine cliente `WinCLI`, accédez à `www.test.local` pour tester la connexion.
9. Mettez hors ligne les serveurs `Win2012ND1` et `Win2012ND2` pour vérifier que `WinCoreND3` continue de servir les requêtes.
10. Créez un script PowerShell pour tester automatiquement le basculement et la répartition de charge NLB.

---

# 7. Conclusion

Ce laboratoire vous permet de comprendre comment configurer et gérer un cluster NLB sous Windows Server, en utilisant des serveurs Windows Core pour une meilleure efficacité. Vous avez configuré des adresses IP dédiées pour le NLB et testé la redondance du cluster en simulant une panne. En maîtrisant ces concepts, vous êtes mieux préparé pour mettre en place des architectures réseau tolérantes aux pannes dans des environnements de production.
