

# Configuration 1 : 1 VLAN et 1 Carte Réseau (Configuration Simple)

- **VLAN** :
  - **VLAN 100** : Utilisé pour la gestion, le trafic de répartition de charge (NLB), et l'accès Internet.

- **Carte Réseau** :
  - **Carte Réseau Unique** : Connectée au VLAN unique (VLAN 100), partageant la gestion, NLB, et l'accès Internet.

- **Utilisation** : Idéal pour des tests simples dans un environnement de laboratoire, mais manque de séparation de trafic et de sécurité pour une utilisation en production.

```
                           VLAN 100 (Segment Unique)
                                +-----------+
                                |   Switch  |
                                +-----------+
                                     |
                 ---------------------------------------------------
                 |                |                 |               |
           +------------+   +------------+   +------------+    +-----------+
           | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |    | Contrôleur|
           | IP: 192.   |   | IP: 192.   |   | IP: 192.   |    | de Domaine|
           | 168.0.1    |   | 168.0.2    |   | 168.0.3    |    | IP: 192.  |
           +------------+   +------------+   +------------+    | 168.0.14  |
                                                                +-----------+

                             Adresse IP Virtuelle du Cluster (VIP)
                                  ClusterWeb.test.local
                                     192.168.0.111
```

---

# Configuration 2 : 2 VLANs et 2 Cartes Réseaux

- **VLANs** :
  - **VLAN 100 (Gestion)** : Utilisé pour l'administration et la gestion des serveurs.
  - **VLAN 200 (NLB)** : Utilisé pour le trafic de répartition de charge entre les serveurs.

- **Cartes Réseaux** :
  - **Carte Réseau 1** : Connectée au VLAN 100 (Gestion).
  - **Carte Réseau 2** : Connectée au VLAN 200 (NLB).

- **Utilisation** : Convient pour un environnement NLB interne sans accès Internet direct pour chaque serveur.

```
                           VLAN 100 (Gestion)
                               +-----------+
                               |   Switch  |
                               +-----------+
                                    |
                 -------------------------------------
                 |                |                 |
           +------------+   +------------+   +------------+
           | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |
           | Gestion:   |   | Gestion:   |   | Gestion:   |
           | 192.168.0.1|   | 192.168.0.2|   | 192.168.0.3|
           +------------+   +------------+   +------------+

                 VLAN 200 (NLB)
                    +-----------+
                    |   Switch  |
                    +-----------+
                         |
        ----------------------------------------------
        |                |                 |
  +------------+   +------------+   +------------+
  | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |
  | NLB:       |   | NLB:       |   | NLB:       |
  | 192.168.0.11|   | 192.168.0.22|   | 192.168.0.33|
  +------------+   +------------+   +------------+

                    Adresse IP Virtuelle du Cluster NLB (VIP)
                                ClusterWeb.test.local
                                     192.168.0.111
```

---

# Configuration 3 : 3 VLANs et 3 Cartes Réseaux

- **VLANs** :
  - **VLAN 100 (Gestion)** : Utilisé pour la gestion et l'administration.
  - **VLAN 200 (NLB)** : Utilisé pour le trafic de répartition de charge.
  - **Internet VLAN** (Bridge) : Permet un accès direct à Internet pour chaque serveur.

- **Cartes Réseaux** :
  - **Carte Réseau 1** : Connectée au VLAN 100 (Gestion).
  - **Carte Réseau 2** : Connectée au VLAN 200 (NLB).
  - **Carte Réseau 3** : Connectée au VLAN Internet (Bridge).

- **Utilisation** : Recommandé si chaque serveur du cluster doit avoir un accès direct à Internet.

```
                           VLAN 100 (Gestion)
                               +-----------+
                               |   Switch  |
                               +-----------+
                                    |
                 -------------------------------------
                 |                |                 |
           +------------+   +------------+   +------------+
           | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |
           | Gestion:   |   | Gestion:   |   | Gestion:   |
           | 192.168.0.1|   | 192.168.0.2|   | 192.168.0.3|
           +------------+   +------------+   +------------+

                 VLAN 200 (NLB)
                    +-----------+
                    |   Switch  |
                    +-----------+
                         |
        ----------------------------------------------
        |                |                 |
  +------------+   +------------+   +------------+
  | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |
  | NLB:       |   | NLB:       |   | NLB:       |
  | 192.168.0.11|   | 192.168.0.22|   | 192.168.0.33|
  +------------+   +------------+   +------------+

                Réseau Internet (Bridge)
                         |
                   +-------------+
                   |   Routeur   |
                   +-------------+
                         |
                     (Internet)
```

---

### Tableau Récapitulatif

| Configuration                       | Nombre de VLANs            | Nombre de Cartes Réseaux | Utilisation                                        |
|-------------------------------------|----------------------------|--------------------------|----------------------------------------------------|
| **Configuration 1**                 | 1 VLAN (Segment Unique)    | 1                        | Tests simples en laboratoire, sans séparation de trafic |
| **Configuration 2**                 | 2 VLANs (Gestion, NLB)     | 2                        | Environnement NLB interne sans accès Internet pour chaque serveur |
| **Configuration 3**                 | 3 VLANs (Gestion, NLB, Internet) | 3                | NLB avec accès Internet direct pour chaque serveur |

---

En fonction de vos besoins, choisissez la configuration qui convient le mieux à votre environnement, en tenant compte de la sécurité, de la complexité de la configuration et de l'accès à Internet.
