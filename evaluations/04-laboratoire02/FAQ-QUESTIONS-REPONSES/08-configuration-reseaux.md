# Explication de la Configuration Réseau

1. **Routeur/Internet** : Dans un environnement VMware ou VirtualBox, vous avez besoin d'une connexion **NAT** ou **Bridge** pour permettre à vos machines virtuelles d'accéder à Internet. Le **routeur** représenté dans le schéma peut être simulé par la fonctionnalité de réseau NAT de VMware, qui permet à toutes les machines d'accéder à Internet via l'adresse IP de l'hôte.

2. **Nombre de Cartes Réseau par Machine** :
   - **Chaque machine du cluster NLB** (Win2012ND1, Win2012ND2, et WinCoreND3) aura **deux cartes réseau** :
     - **Carte 1 (IP de gestion)** : utilisée pour l'administration et la gestion des serveurs.
     - **Carte 2 (IP NLB)** : utilisée pour la répartition de charge (NLB).
   - **Le Contrôleur de Domaine (DNS)** aura également **une seule carte réseau** pour gérer les requêtes DNS et l'administration du domaine.
   - **Machine Cliente Windows 10 (WinCLI)** : Cette machine aura **une carte réseau** pour se connecter au cluster et tester la répartition de charge.

3. **Connexion à Internet** : 
   - Si vous souhaitez que toutes les machines puissent accéder à Internet, configurez une carte réseau en **NAT** ou en mode **Bridge** pour chaque machine sur VMware.
   - La configuration **NAT** est préférable pour un laboratoire, car elle permet aux machines d'accéder à Internet via l'IP de l'hôte sans exposer directement les machines virtuelles à l'extérieur.

### Tableau ASCII de la Configuration des Cartes Réseau

```
+----------------------+--------------------------+-------------------------+
| Machine             | Carte Réseau 1           | Carte Réseau 2          |
+----------------------+--------------------------+-------------------------+
| Win2012ND1          | IP de Gestion : 192.168.0.1 | IP NLB : 192.168.0.11  |
|                     | Type : NAT ou Bridge      | Type : Réseau interne   |
+----------------------+--------------------------+-------------------------+
| Win2012ND2          | IP de Gestion : 192.168.0.2 | IP NLB : 192.168.0.22  |
|                     | Type : NAT ou Bridge      | Type : Réseau interne   |
+----------------------+--------------------------+-------------------------+
| WinCoreND3          | IP de Gestion : 192.168.0.3 | IP NLB : 192.168.0.33  |
|                     | Type : NAT ou Bridge      | Type : Réseau interne   |
+----------------------+--------------------------+-------------------------+
| Contrôleur de Domaine | IP de Gestion : 192.168.0.14 |                      |
| (DNS)               | Type : NAT ou Bridge      |                         |
+----------------------+--------------------------+-------------------------+
| WinCLI (Client)     | IP : Dynamique ou fixe    |                         |
|                     | Type : NAT ou Bridge      |                         |
+----------------------+--------------------------+-------------------------+
```

### Détails des Types de Connexion

- **Carte Réseau 1 (Gestion)** : Configurez cette carte en **NAT** ou **Bridge** pour permettre aux machines de communiquer avec l'hôte et d'avoir un accès à Internet si nécessaire. Cette configuration assure une connectivité globale pour la gestion et l'administration.

- **Carte Réseau 2 (NLB)** : Cette carte est utilisée exclusivement pour le trafic NLB entre les serveurs du cluster et doit être configurée en **Réseau Interne** (Internal Network) pour que seul le trafic du cluster soit transmis sur cette carte.

### Récapitulatif

- **Chaque serveur du cluster NLB** a deux cartes réseau : une pour la gestion (NAT ou Bridge) et une pour le trafic NLB (Réseau Interne).
- **Le Contrôleur de Domaine** a une seule carte réseau pour la gestion et la connexion DNS.
- **La machine cliente Windows 10 (WinCLI)** a une carte réseau pour tester l'accès au cluster.

Cette configuration vous permet de simuler un environnement de répartition de charge avec VMware, tout en assurant que chaque serveur peut être administré individuellement et que le trafic NLB est isolé.
