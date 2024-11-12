# Guide des Configurations Réseau pour un Cluster NLB

# Introduction

Ce document présente quatre configurations réseau possibles pour un cluster NLB (Network Load Balancer) dans un environnement de laboratoire VMware. Chacune est analysée en termes de faisabilité pour le basculement (failover), sécurité, et complexité de mise en œuvre. Le choix de configuration dépend des besoins spécifiques, notamment en termes de séparation des trafics et de basculement.

---

# Configuration 1 : 1 VLAN et 1 Carte Réseau (Configuration Simple)

### Description

Dans cette configuration, toutes les machines (serveurs, contrôleur de domaine, client) utilisent une seule carte réseau et un VLAN unique. Ce réseau unique est utilisé pour la gestion, la répartition de charge (NLB), et l'accès à Internet, si nécessaire.

### Tableau de Configuration

| Machine              | Carte Réseau (VLAN Unique) | Adresse IP      |
|----------------------|----------------------------|------------------|
| Win2012ND1           | VLAN 100                   | 192.168.0.1/24  |
| Win2012ND2           | VLAN 100                   | 192.168.0.2/24  |
| WinCoreND3           | VLAN 100                   | 192.168.0.3/24  |
| Contrôleur de Domaine| VLAN 100                   | 192.168.0.14/24 |
| WinCLI (Client)      | VLAN 100                   | Dynamique ou fixe|
| Cluster VIP (NLB)    | VLAN 100                   | 192.168.0.111/24|

### Fonctionnalité de Basculement

- **Cluster de Basculement** : **Non fonctionnel**. Le basculement n'est pas réalisable dans cette configuration en raison de la limitation d'un seul chemin de communication partagé entre la gestion, NLB, et Internet.

---

# Configuration 2 : 1 VLAN et 2 Cartes Réseaux

### Description

Dans cette configuration, chaque machine dispose de deux cartes réseau :
- Les deux cartes sont connectées au même VLAN unique, mais l’une est réservée au trafic de gestion et l’autre au trafic de répartition de charge.

### Tableau de Configuration

| Machine              | Carte Réseau 1 (VLAN Unique - Gestion) | Carte Réseau 2 (VLAN Unique - NLB) |
|----------------------|----------------------------------------|------------------------------------|
| Win2012ND1           | 192.168.0.1/24                        | 192.168.0.11/24                   |
| Win2012ND2           | 192.168.0.2/24                        | 192.168.0.22/24                   |
| WinCoreND3           | 192.168.0.3/24                        | 192.168.0.33/24                   |
| Contrôleur de Domaine| 192.168.0.14/24                       | Non requis                        |
| WinCLI (Client)      | Dynamique ou fixe                     | Non requis                        |
| Cluster VIP (NLB)    | N/A                                   | 192.168.0.111/24                  |

### Fonctionnalité de Basculement

- **Cluster de Basculement** : **Partiellement fonctionnel**. Bien que cette configuration soit encore limitée par un seul VLAN, l'utilisation de deux cartes réseau par serveur peut permettre une redirection minimale en cas de défaillance d'une carte ou d'un chemin de trafic, mais il n'y a pas de séparation stricte entre les trafics NLB et gestion.

---

# Configuration 3 : 2 VLANs et 2 Cartes Réseaux (Configuration Intermédiaire)

### Description

Dans cette configuration, chaque machine dispose de deux cartes réseau, chacune connectée à un VLAN distinct :
- Une carte réseau pour le réseau de gestion.
- Une carte réseau pour le réseau NLB, réservé à la répartition de charge.

### Tableau de Configuration

| Machine              | Carte Réseau 1 (Gestion - VLAN 100) | Carte Réseau 2 (NLB - VLAN 200) |
|----------------------|-------------------------------------|----------------------------------|
| Win2012ND1           | 192.168.0.1/24                     | 192.168.1.11/24                 |
| Win2012ND2           | 192.168.0.2/24                     | 192.168.1.22/24                 |
| WinCoreND3           | 192.168.0.3/24                     | 192.168.1.33/24                 |
| Contrôleur de Domaine| 192.168.0.14/24                    | Non requis                      |
| WinCLI (Client)      | Dynamique ou fixe                  | Non requis                      |
| Cluster VIP (NLB)    | N/A                                | 192.168.1.111/24                |

### Fonctionnalité de Basculement

- **Cluster de Basculement** : **Fonctionnel**. Grâce à la séparation des VLANs entre gestion et NLB, le trafic NLB peut être redirigé en cas de panne d’un serveur. Cette configuration permet un basculement plus fiable comparé aux configurations précédentes.

---

# Configuration 4 : 3 VLANs et 3 Cartes Réseaux (Configuration Avancée)

### Description

Chaque machine est configurée avec trois cartes réseau :
- Une carte pour le réseau de gestion.
- Une autre pour le réseau NLB (répartition de charge).
- Une troisième pour l'accès Internet direct via un routeur ou pare-feu.

### Tableau de Configuration

| Machine              | Carte Réseau 1 (Gestion - VLAN 100) | Carte Réseau 2 (NLB - VLAN 200) | Carte Réseau 3 (Internet - VLAN 300) |
|----------------------|-------------------------------------|----------------------------------|---------------------------------------|
| Win2012ND1           | 192.168.0.1/24                     | 192.168.1.11/24                 | Obtenue via le routeur                |
| Win2012ND2           | 192.168.0.2/24                     | 192.168.1.22/24                 | Obtenue via le routeur                |
| WinCoreND3           | 192.168.0.3/24                     | 192.168.1.33/24                 | Obtenue via le routeur                |
| Contrôleur de Domaine| 192.168.0.14/24                    | Non requis                      | Obtenue via le routeur                |
| WinCLI (Client)      | Dynamique ou fixe                  | Non requis                      | Obtenue via le routeur                |
| Cluster VIP (NLB)    | N/A                                | 192.168.1.111/24                | Non requis                           |

### Fonctionnalité de Basculement

- **Cluster de Basculement** : **Parfaitement fonctionnel**. Cette configuration avec trois VLANs distincts offre une isolation complète des trafics, permettant une redirection efficace en cas de panne d'un serveur, ainsi qu'un basculement optimal pour la haute disponibilité.

---

## Tableau Récapitulatif en ASCII

```
+------------------+--------------------------+--------------------+---------------------------+------------------------+
| Configuration    | Nombre de VLANs          | Nombre de Cartes   | Fonctionnalité Basculement | Remarques              |
|                  |                          | Réseau             |                           |                        |
+------------------+--------------------------+--------------------+---------------------------+------------------------+
| Config 1 (Simple)| 1                        | 1                  | Non fonctionnel           | Utilisée pour tests    |
|                  |                          |                    |                           | simples, pas de        |
|                  |                          |                    |                           | séparation des trafics |
+------------------+--------------------------+--------------------+---------------------------+------------------------+
| Config 2         | 1                        | 2                  | Partiellement fonctionnel | Deux cartes pour       |
|                  |                          |                    |                           | gestion et NLB, mais   |
|                  |                          |                    |                           | pas de séparation VLAN |
+------------------+--------------------------+--------------------+---------------------------+------------------------+
| Config 3         | 2                        | 2                  | Fonctionnel               | Séparation gestion /   |
| (Intermédiaire)  |                          |                    |                           | NLB, meilleure         |
|                  |                          |                    |                           | redondance             |
+------------------+--------------------------+--------------------+---------------------------+------------------------+
| Config 4         | 3                        | 3                  | Parfaitement fonctionnel  | Isolation complète des |
| (Avancée)        |                          |                    |                           | trafics, haute         |
|                  |                          |                    |                           | disponibilité          |
+------------------+--------------------------+--------------------+---------------------------+------------------------+
```

---

## Conclusion

- **Configuration 1** (1 VLAN et 1 Carte Réseau) : Convient pour les tests simples, sans possibilité de basculement.
- **Configuration 2** (1 VLAN et 2 Cartes Réseaux) : Ajoute une certaine redondance grâce à deux cartes réseau, mais manque d’une séparation stricte des trafics.
- **Configuration 3** (2 VLANs et 2 Cartes Réseaux) : Fournit un basculement fonctionnel avec une séparation du trafic NLB et gestion.


- **Configuration 4** (3 VLANs et 3 Cartes Réseaux) : Recommandée pour une isolation complète et une haute disponibilité, idéale pour un cluster de basculement sécurisé et fiable.

Le choix de la configuration doit être adapté aux besoins de basculement, de redondance et de séparation des trafics pour assurer un équilibre entre sécurité et simplicité de gestion.
