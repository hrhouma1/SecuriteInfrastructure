# **Question #01 :**  
*Dans le cadre du laboratoire sur la sécurité de l'infrastructure, pourriez-vous clarifier si tous les serveurs Windows sont en mode Core ou si cela concerne uniquement le serveur WinCoreND3 ? De plus, quel serveur est configuré en tant que contrôleur de domaine et lequel assure la fonction DNS ?*

![image](https://github.com/user-attachments/assets/e3f27bf1-ab32-4d12-a173-4be9fe595b90)


**Réponse :**  
Dans ce laboratoire, la configuration des serveurs Windows est précisée comme suit :

1. **Mode Core** :
   - Seul le serveur **WinCoreND3** est explicitement configuré en mode Core. Le mode Core signifie que le serveur fonctionne sans interface graphique, rendant le système plus léger et sécurisé pour des tâches spécifiques, comme celles de répartition de charge.
   - Les autres serveurs, **Win2012ND1** et **Win2012ND2**, ne sont pas obligatoirement en mode Core. *On peut considérer que les autres serveurs sont configurés avec une interface graphique*, bien que ce ne soit pas une obligation. Le choix de l’interface dépend des besoins de gestion et d’administration pour le laboratoire.

2. **Rôle du Contrôleur de Domaine et du DNS** :
   - Le **contrôleur de domaine** est le serveur qui gère le domaine `test.local`. Il assure la gestion centralisée des identités et des accès pour les utilisateurs et machines du domaine. C’est lui qui permet aux autres serveurs d’être intégrés dans un environnement sécurisé où les ressources et permissions sont administrées de manière centralisée.
   - Ce même serveur héberge également le **service DNS** pour le domaine `test.local`. Le DNS (Domain Name System) est essentiel pour traduire les noms de domaine (par exemple, `ClusterWeb.test.local`) en adresses IP, permettant ainsi aux clients et serveurs de se connecter de manière conviviale sans utiliser les adresses IP directement. Dans le schéma, ce serveur est représenté à droite, connecté au réseau avec le nom `test.local`.

En résumé, **WinCoreND3** est le seul serveur en mode Core, et le **contrôleur de domaine** gère également les fonctions DNS pour l’environnement `test.local`. Cette configuration permet une gestion centralisée des accès tout en assurant une résolution de noms efficace pour le cluster NLB.


---------------
# Annexe : Cluster NLB
---------------


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

# Explication des éléments :

- **Internet** : Représente la connexion externe depuis laquelle les utilisateurs peuvent accéder au service.
- **Switch** : Permet la connexion de tous les serveurs au même réseau local.
- **Serveurs 1, 2 et 3** : Ce sont les trois nœuds du cluster NLB.
  - **Serveur 1** : `Win2012ND1` avec l'IP de gestion 192.168.0.1 et l'IP NLB 192.168.0.11.
  - **Serveur 2** : `Win2012ND2` avec l'IP de gestion 192.168.0.2 et l'IP NLB 192.168.0.22.
  - **Serveur 3** : `WinCoreND3` avec l'IP de gestion 192.168.0.3 et l'IP NLB 192.168.0.33.
  - Chaque serveur a un identifiant unique pour le cluster (1, 2, ou 3).
- **Contrôleur de Domaine (DNS)** : Ce serveur gère le domaine `test.local` et la résolution DNS pour le cluster.
- **Adresse IP du Cluster (VIP)** : `ClusterWeb.test.local` avec l'IP 192.168.0.111, utilisée par le NLB pour répartir la charge entre les serveurs.

