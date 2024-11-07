#  Scénarios de Configuration Réseau pour un Cluster NLB

## Introduction

Ce document présente deux scénarios de configuration réseau pour un cluster NLB dans un laboratoire VMware :

1. **Scénario avec deux cartes réseau** : Une carte pour le réseau de gestion et une autre pour le réseau de répartition de charge (NLB).
2. **Scénario avec trois cartes réseau** : Une carte pour le réseau de gestion, une pour le réseau NLB, et une troisième pour permettre un accès Internet via un routeur en mode Bridge.

Ces scénarios sont expliqués de manière exhaustive pour un débutant, avec des schémas, des tables de configuration, et des explications pas à pas.

---

## Scénario 1 : Cluster NLB avec Deux Cartes Réseau (Gestion et NLB)

### Description

Dans ce scénario, chaque serveur du cluster est configuré avec deux cartes réseau :
- **Carte Réseau 1 (Gestion - VLAN 100)** : Utilisée pour la gestion et l'administration des serveurs. 
- **Carte Réseau 2 (NLB - VLAN 200)** : Utilisée pour le trafic de répartition de charge entre les serveurs du cluster.

### Objectif

Cette configuration est suffisante si l'objectif est de créer un cluster NLB interne **sans accès direct à Internet pour chaque serveur**. Seules les machines contrôleur de domaine et client (WinCLI) auront besoin d'un accès à Internet pour des tests.

### Configuration Réseau dans VMware

1. **Créer des réseaux personnalisés dans VMware** :
   - **VLAN 100 (Gestion)** : Configurez un réseau virtuel pour la gestion.
   - **VLAN 200 (NLB)** : Configurez un autre réseau virtuel pour la répartition de charge (NLB).
   
2. **Attribuer les adresses IP** :
   - Configurez les adresses IP de gestion dans le VLAN 100.
   - Configurez les adresses IP NLB dans le VLAN 200.

### Tableau de Configuration

| Machine              | Carte Réseau 1 (Gestion - VLAN 100) | Carte Réseau 2 (NLB - VLAN 200) |
|----------------------|-------------------------------------|----------------------------------|
| Win2012ND1           | IP: 192.168.0.1/24                 | IP NLB: 192.168.0.11/24         |
| Win2012ND2           | IP: 192.168.0.2/24                 | IP NLB: 192.168.0.22/24         |
| WinCoreND3           | IP: 192.168.0.3/24                 | IP NLB: 192.168.0.33/24         |
| Contrôleur de Domaine| IP: 192.168.0.14/24                | Non requis                      |
| WinCLI (Client)      | IP dynamique ou fixe               | Non requis                      |
| Cluster VIP (NLB)    | VIP: 192.168.0.111/24              | (Adresse virtuelle NLB)         |

### Étapes de Configuration

1. **Créez les réseaux VLAN 100 et VLAN 200 dans VMware** :
   - VLAN 100 pour le réseau de gestion, VLAN 200 pour le réseau NLB.
   
2. **Attribuez les cartes réseau à chaque machine** :
   - **Carte Réseau 1** : Assignez à chaque machine une adresse IP dans le VLAN 100 pour la gestion.
   - **Carte Réseau 2** : Assignez une adresse IP dans le VLAN 200 pour la répartition de charge.

3. **Configurer le DNS et le Contrôleur de Domaine** :
   - Sur le contrôleur de domaine (192.168.0.14), configurez le service DNS pour le domaine `test.local`.
   - Ajoutez un enregistrement A pour `ClusterWeb.test.local` pointant vers l'adresse VIP du cluster (192.168.0.111).

### Schéma ASCII du Scénario 1

```
                           VLAN 100 (Gestion)
                               +-----------+
                               | Switch    |
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
                    | Switch    |
                    +-----------+
                         |
        ----------------------------------------------
        |                |                 |
  +------------+   +------------+   +------------
  | Win2012ND1 |   | Win2012ND2 |   | WinCoreND3 |
  | NLB:       |   | NLB:       |   | NLB:       |
  | 192.168.0.11|   | 192.168.0.22|   | 192.168.0.33|
  +------------+   +------------+   +------------+

                    Adresse IP Virtuelle du Cluster NLB (VIP)
                                ClusterWeb.test.local
                                     192.168.0.111
```

### Avantages et Inconvénients du Scénario 1

- **Avantages** :
  - Plus simple à configurer et à gérer, car chaque serveur n'a que deux cartes réseau.
  - Réduit la surface d'exposition en limitant l'accès Internet aux seuls serveurs nécessaires (Contrôleur de domaine et client WinCLI).
  - Favorise la sécurité en isolant le réseau de gestion et le réseau de répartition de charge.

- **Inconvénients** :
  - Les serveurs du cluster (Win2012ND1, Win2012ND2, WinCoreND3) n'ont pas d'accès direct à Internet. Cela peut être une limitation si vous avez besoin de mises à jour ou d'autres ressources en ligne directement depuis ces serveurs.

---

## Scénario 2 : Cluster NLB avec Trois Cartes Réseau (Gestion, NLB et Internet)

### Description

Dans ce scénario, chaque serveur du cluster est configuré avec trois cartes réseau :
- **Carte Réseau 1 (Gestion - VLAN 100)** : Utilisée pour la gestion et l'administration des serveurs.
- **Carte Réseau 2 (NLB - VLAN 200)** : Utilisée pour le trafic de répartition de charge entre les serveurs du cluster.
- **Carte Réseau 3 (Internet - Bridge)** : Permet un accès direct à Internet via le routeur.

### Objectif

Ce scénario est utilisé si **chaque serveur du cluster doit pouvoir accéder à Internet individuellement** pour des mises à jour, des téléchargements, ou des tests nécessitant un accès en ligne.

### Configuration Réseau dans VMware

1. **Créer des réseaux personnalisés dans VMware** :
   - **VLAN 100 (Gestion)** : Réseau virtuel pour la gestion.
   - **VLAN 200 (NLB)** : Réseau virtuel pour la répartition de charge.
   - **Internet (Bridge)** : Connecté au réseau physique de l'hôte pour accéder à Internet.

2. **Attribuer les adresses IP** :
   - Configurez les adresses IP de gestion dans le VLAN 100.
   - Configurez les adresses IP NLB dans le VLAN 200.
   - La carte réseau en mode Bridge obtiendra une adresse IP pour l'Internet via le routeur.

### Tableau de Configuration

| Machine              | Carte Réseau 1 (Gestion - VLAN 100) | Carte Réseau 2 (NLB - VLAN 200) | Carte Réseau 3 (Internet - Bridge) |
|----------------------|-------------------------------------|----------------------------------|-------------------------------------|
| Win2012ND1           | IP: 192.168.0.1/24                 | IP NLB: 192.168.0.11/24         | Obtenue via le routeur             |
| Win2012ND2           | IP: 192.168.0.2/24                 | IP NLB: 192.168.0.22/24         | Obtenue via le routeur             |
| WinCoreND3           | IP: 192.168.0.3/24                 | IP NLB: 192.168.0.33/24         | Obtenue via le routeur             |
| Contrôleur de Domaine| IP: 192.168.0.14/24                | Non requis                      | Obtenue via le routeur             |
| WinCLI (Client)      | Dynamique ou fixe                  | Non requis                      | Obtenue via le routeur             |
| Cluster VIP (NLB)    | VIP: 192.168.0.111/24              | (Adresse virtuelle NLB)         | Non requis                         |

### Étapes de Configuration

1. **Créez les réseaux VLAN 100 (Gestion) et VLAN 200 (NLB) dans VMware** :
   - VLAN 100 pour le réseau de gestion, VLAN 200 pour le réseau NLB.
   
2. **Ajoutez la carte réseau en mode Bridge pour chaque machine** :
   - Configurez cette carte pour obtenir une adresse IP via le routeur, afin de permettre un accès à Internet.

3. **Configurer le DNS et le Contrôleur de Domaine** :
   - Sur le contrôleur de domaine (192.168.0.14), configurez le service DNS pour le domaine `test.local`.
   - Ajoutez un enregistrement A pour `ClusterWeb.test.local` pointant vers l'adresse VIP du cluster (192.168.0.111).

### Schéma ASCII du Scénario 2

```
                           VLAN 100 (Gestion)
                               +-----------+
                               | Switch    |
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
                    | Switch    |
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

### Avantages et Inconvénients du Scénario 2

- **Avantages** :
  - Permet à chaque serveur d’accéder directement à Internet pour les mises à jour et les téléchargements.
  - Utile si des tests nécessitent une connexion Internet sur chaque serveur.

- **Inconvénients** :
  - Configuration plus complexe avec trois cartes réseau par serveur.
  - Augmente la surface d'exposition au réseau, ce qui peut poser des problèmes de sécurité.
  - Un routeur est nécessaire pour fournir une connexion Internet au réseau Bridge.

---

## Comparaison des Scénarios

| Critère                    | Scénario 1 (Deux Cartes Réseau)                    | Scénario 2 (Trois Cartes Réseau)                    |
|----------------------------|----------------------------------------------------|----------------------------------------------------|
| **Nombre de Cartes Réseau** | Deux : Gestion et NLB                              | Trois : Gestion, NLB, et Internet                  |
| **Accès Internet**         | Limité au contrôleur de domaine et au client       | Accès Internet direct pour tous les serveurs       |
| **Sécurité**               | Plus sécurisé : moins de connexions externes       | Moins sécurisé : chaque serveur peut accéder à Internet |
| **Complexité**             | Simple                                             | Plus complexe                                     |
| **Utilisation recommandée**| Scénario sécurisé, accès Internet limité           | Scénario nécessitant des connexions Internet sur chaque serveur |

---

## Conclusion

- **Scénario 1 (Deux Cartes Réseau)** est recommandé si vous cherchez une configuration sécurisée et simple, où seuls le contrôleur de domaine et la machine client ont besoin d’un accès à Internet.
- **Scénario 2 (Trois Cartes Réseau)** est utile si chaque serveur doit accéder à Internet, mais cette configuration est plus complexe et potentiellement moins sécurisée.

En fonction de vos besoins en matière d’accès Internet et de sécurité, vous pouvez choisir entre ces deux configurations pour votre laboratoire NLB.
