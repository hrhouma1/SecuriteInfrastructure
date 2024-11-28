# *Architecture Réseau pour l'Examen Final*

# **Présentation**

L’examen final pratique s’appuie sur une infrastructure réseau basée sur deux réseaux virtuels (VMnet1 et VMnet2) pour simuler un environnement réaliste de gestion de domaine Active Directory et de répartition de charge réseau (NLB). 

---

# 01 - **Diagramme de l’Architecture**

```
                          +-------------------+
                          |      CLIENT       |
                          |   Windows 10 Pro  |
                          |-------------------|
                          |   VMnet1          |
                          | IP: DHCP/Fixe     |
                          +-------------------+
                                 |
                         +-------------------+
                         |     VMnet1        | (Gestion & Domaine)
                         +-------------------+
                          /          |         \
           +----------------+ +----------------+ +----------------+
           |      DC         | |      SRV1      | |      SRV2      |
           | Windows Server  | | Windows Server | | Windows Server |
           | 2019 (AD DS)    | | 2019           | | 2019           |
           |-----------------| |----------------| |----------------|
           | VMnet1          | | VMnet1         | | VMnet1         |
           | IP: 192.168.1.5 | | IP: 192.168.1.10| | IP: 192.168.1.20|
           | DNS/DHCP        | | VIP: 192.168.2.100 (via NLB)      |
           +-----------------+ +----------------+ +----------------+
                                 |                 |
                          +-------------------+ +-------------------+
                          |     VMnet2        | |     VMnet2        |
                          +-------------------+ +-------------------+
                          | IP: 192.168.2.10  | | IP: 192.168.2.20  |
                          |   (Trafic NLB)    | |   (Trafic NLB)    |
                          +-------------------+ +-------------------+
                                 |                 |
                          +-------------------------------+
                          |         VIP (Cluster NLB)     |
                          |       IP: 192.168.2.100       |
                          +-------------------------------+
```

---

# 02 - **Tableau des Rôles et Adresses IP**

| **Nom**         | **Réseau (VMnet)** | **Adresse IP**         | **Rôle**                              | **Fonctionnalités**                                                                                |
|------------------|--------------------|-------------------------|----------------------------------------|---------------------------------------------------------------------------------------------------|
| **DC**           | VMnet1            | 192.168.1.5            | Contrôleur de domaine (AD DS), DNS    | - Gère les utilisateurs, groupes et ordinateurs dans le domaine `exam.local`.<br>- Fournit DNS.  |
| **SRV1**         | VMnet1            | 192.168.1.10           | Nœud 1 du cluster NLB (gestion)       | - IIS personnalisé.<br>- Participe au domaine `exam.local`.<br>- Configure une interface sur VMnet2. |
|                  | VMnet2            | 192.168.2.10           | Trafic NLB                             | - Reçoit les requêtes via la **VIP (192.168.2.100)**.                                             |
| **SRV2**         | VMnet1            | 192.168.1.20           | Nœud 2 du cluster NLB (gestion)       | - IIS personnalisé.<br>- Participe au domaine `exam.local`.<br>- Configure une interface sur VMnet2. |
|                  | VMnet2            | 192.168.2.20           | Trafic NLB                             | - Reçoit les requêtes via la **VIP (192.168.2.100)**.                                             |
| **VIP (Cluster)**| VMnet2            | 192.168.2.100          | Adresse virtuelle partagée (NLB)      | - Répartit les requêtes entre SRV1 et SRV2.<br>- Assure la haute disponibilité et le basculement.|
| **CLIENT**       | VMnet1            | DHCP ou 192.168.1.50   | Machine cliente                        | - Teste les accès à `http://192.168.2.100` et `http://192.168.1.x`.<br>- Participe au domaine.   |

---

# 03 - **Description des Réseaux**

1. **VMnet1 (Gestion & Domaine) :**
   - Interconnecte le **Contrôleur de Domaine (DC)**, les serveurs (SRV1 et SRV2), et le client.
   - Fournit les services DNS/DHCP nécessaires pour la gestion du domaine.

2. **VMnet2 (Trafic NLB) :**
   - Connecte uniquement les serveurs SRV1 et SRV2 pour assurer le trafic lié au cluster NLB.
   - Permet une isolation complète entre le trafic NLB et le réseau de gestion.

---

# 04 - **Détails des Composants**

## **1. Contrôleur de Domaine (DC)**
- Fournit Active Directory Domain Services (AD DS) pour le domaine `exam.local`.
- Gère les enregistrements DNS pour la résolution des noms.
- Optionnellement, distribue des adresses IP via DHCP.

## **2. Serveurs du Cluster NLB (SRV1 & SRV2)**
- Hébergent une page IIS personnalisée (exemple : *"Bienvenue sur SRV1"*).
- Configurés en cluster NLB pour répartir les requêtes entre les deux nœuds via l’adresse virtuelle `192.168.2.100`.

## **3. Client (CLIENT)**
- Teste les services IIS sur SRV1 et SRV2.
- Vérifie le bon fonctionnement du cluster NLB.
- Participe au domaine `exam.local`.

---

# 05 - **Instructions pour la Préparation**

1. **Créer les machines virtuelles nécessaires :**
   - Une **machine Windows Server 2019** pour le contrôleur de domaine.
   - Deux **machines Windows Server 2019** pour SRV1 et SRV2.
   - Une **machine Windows 10 Professionnel** pour le client.

2. **Configurer les réseaux (VMnets) :**
   - Assigner **VMnet1** pour la gestion et le domaine.
   - Assigner **VMnet2** pour le trafic NLB uniquement.

3. **Configurer les adresses IP :**
   - Utilisez les adresses précisées dans le tableau.

4. **Tester la communication réseau :**
   - Vérifiez que toutes les machines sur **VMnet1** peuvent se pinger entre elles.
   - Assurez-vous que SRV1 et SRV2 communiquent correctement sur **VMnet2**.

5. **Vérifier les services :**
   - DNS doit résoudre les noms des serveurs et du domaine.
   - Le client doit pouvoir accéder à `http://192.168.2.100`.

---

# **Validation**
- Avant l’examen, confirmez que les machines sont opérationnelles et que les configurations réseau sont fonctionnelles.
- Testez l’accès au domaine et au cluster NLB pour garantir le bon fonctionnement.

-----------------
# Bonus :
-----------------

- *Rédigez un document clair et structuré détaillant les étapes de votre préparation, puis envoyez-le avant la date de l'examen. Vous avez également le droit de modifier les adresses IP, à condition de fournir une cartographie précise et détaillée de votre configuration.*

