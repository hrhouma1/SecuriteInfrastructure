# Architecture de l'examen final

----------------

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
           | IP: 192.168.1.1 | | IP: 192.168.1.10| | IP: 192.168.1.20|
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

### **Explications des réseaux et interfaces**

1. **Réseaux (VMnets) :**
   - **VMnet1 (Gestion et Domaine)** :
     - Gère les communications entre le **Contrôleur de Domaine (DC)**, les serveurs (SRV1 et SRV2), et le client (CLIENT).
     - IP utilisées : `192.168.1.x`.
   - **VMnet2 (Trafic NLB)** :
     - Utilisé exclusivement pour le trafic du cluster NLB.
     - IP utilisées : `192.168.2.x`.

2. **Détails des Machines et Rôles :**
   - **DC** :
     - Connecté uniquement à **VMnet1**.
     - Fournit les services de **DNS, DHCP, et Active Directory**.
   - **SRV1 et SRV2** :
     - Chaque serveur a deux interfaces réseau :
       - **VMnet1** : Pour la gestion et la communication avec le domaine.
       - **VMnet2** : Pour le trafic NLB.
     - Partagent une adresse IP virtuelle (VIP) sur **VMnet2** : `192.168.2.100`.
   - **CLIENT** :
     - Connecté à **VMnet1** uniquement.
     - Utilisé pour tester l’accès au cluster NLB (`http://192.168.2.100`) et au domaine.

3. **VIP (Virtual IP)** :
   - L’adresse virtuelle du cluster NLB est configurée sur **VMnet2** : `192.168.2.100`.
   - Gérée par les serveurs SRV1 et SRV2 pour la répartition de charge.

---

### **Table des Adresses et Rôles**

| **Nom**         | **VMnet** | **Adresse IP**         | **Rôle**                              |
|------------------|-----------|-------------------------|----------------------------------------|
| **DC**           | VMnet1    | 192.168.1.1            | Contrôleur de domaine (AD DS), DNS    |
| **SRV1**         | VMnet1    | 192.168.1.10           | Nœud 1 du cluster NLB (gestion)       |
|                  | VMnet2    | 192.168.2.10           | Trafic NLB                             |
| **SRV2**         | VMnet1    | 192.168.1.20           | Nœud 2 du cluster NLB (gestion)       |
|                  | VMnet2    | 192.168.2.20           | Trafic NLB                             |
| **VIP (Cluster)**| VMnet2    | 192.168.2.100          | Adresse virtuelle partagée (NLB)      |
| **CLIENT**       | VMnet1    | DHCP ou 192.168.1.50   | Machine cliente pour tests et accès   |



----------------
# ANNEXE :Architecture Réseau, Composants et Connexions
----------------


### **Table Détailée de l’Architecture Réseau**

Voici une table améliorée détaillant chaque composant, son rôle, son réseau, ses connexions, et ses fonctionnalités attendues.

| **Nom**           | **Réseau (VMnet)** | **Adresse IP**       | **Rôle**                              | **Fonctionnalité / Membres**                                                                                      |
|--------------------|--------------------|-----------------------|----------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **DC**            | **VMnet1**         | `192.168.1.1`        | **Contrôleur de Domaine (AD DS)**      | - Fournit le service Active Directory.<br>- Gère les utilisateurs, groupes et ordinateurs du domaine `exam.local`.<br>- Fournit DNS et (optionnel) DHCP.|
| **SRV1**          | **VMnet1**         | `192.168.1.10`       | **Nœud 1 du cluster NLB**              | - Participe au domaine `exam.local`.<br>- Fournit une page IIS personnalisée.<br>- Configure une interface sur VMnet2 pour le trafic NLB.              |
|                   | **VMnet2**         | `192.168.2.10`       | Trafic NLB                             | - Reçoit les requêtes via la **VIP (192.168.2.100)** partagée avec SRV2.                                         |
| **SRV2**          | **VMnet1**         | `192.168.1.20`       | **Nœud 2 du cluster NLB**              | - Participe au domaine `exam.local`.<br>- Fournit une page IIS personnalisée.<br>- Configure une interface sur VMnet2 pour le trafic NLB.              |
|                   | **VMnet2**         | `192.168.2.20`       | Trafic NLB                             | - Reçoit les requêtes via la **VIP (192.168.2.100)** partagée avec SRV1.                                         |
| **VIP (Cluster)** | **VMnet2**         | `192.168.2.100`      | Adresse virtuelle partagée (NLB)       | - Répartit les requêtes entre SRV1 et SRV2.<br>- Permet la haute disponibilité et le basculement en cas de panne.|
| **CLIENT**        | **VMnet1**         | DHCP ou `192.168.1.50`| **Machine cliente pour tests**         | - Utilisé pour vérifier les accès à la VIP (`http://192.168.2.100`) et aux serveurs IIS individuels.<br>- Participe au domaine `exam.local`.           |

---

### **Détails sur les Composants et Connexions**

#### **1. Contrôleur de Domaine (DC)**
- **Rôle** :
  - Cœur du domaine Active Directory `exam.local`.
  - Gère la base de données des objets Active Directory (utilisateurs, groupes, ordinateurs).
  - Fournit les services DNS pour la résolution des noms.
  - Optionnel : Peut être configuré pour attribuer des adresses IP via DHCP.

- **Fonctionnalités principales :**
  - **Gestion des membres du domaine :**
    - Les machines SRV1, SRV2, et CLIENT doivent être **membres du domaine** `exam.local`.
    - Chaque machine est ajoutée au domaine via `Add-Computer` ou l'interface graphique.
  - **DNS :**
    - Gère les enregistrements pour que les machines puissent résoudre les noms (exemple : `SRV1.exam.local`).
  - **Services optionnels :**
    - DHCP (peut être activé pour attribuer dynamiquement les IP dans le réseau VMnet1).

---

#### **2. Serveurs du Cluster (SRV1 & SRV2)**
- **Rôle :**
  - Partagent la charge des requêtes via le **cluster NLB**.
  - Fournissent des pages IIS personnalisées pour vérifier le fonctionnement du cluster.

- **Fonctionnalités principales :**
  - **Participation au domaine :**
    - Chaque serveur est ajouté au domaine `exam.local` pour la gestion centralisée.
  - **IIS (Internet Information Services) :**
    - SRV1 et SRV2 hébergent une page personnalisée (ex. : *"Bienvenue sur SRV1"* ou *"Bienvenue sur SRV2"*).
  - **NLB (Network Load Balancing) :**
    - Configuré sur **VMnet2** pour assurer la répartition des requêtes.
    - Les deux serveurs partagent la VIP `192.168.2.100` et répondent aux requêtes via VMnet2.

---

#### **3. VIP (Virtual IP - Cluster NLB)**
- **Rôle :**
  - Adresse virtuelle `192.168.2.100` utilisée pour accéder au cluster.
  - Répartit les requêtes entre SRV1 et SRV2.

- **Trafic :**
  - Exclusivement sur **VMnet2**.
  - Testée depuis la machine CLIENT avec l’URL : `http://192.168.2.100`.

---

#### **4. Machine Cliente (CLIENT)**
- **Rôle :**
  - Permet de tester les services IIS sur SRV1 et SRV2.
  - Vérifie le fonctionnement de la VIP NLB (`192.168.2.100`).
  - Doit être un membre du domaine `exam.local`.

- **Fonctionnalités principales :**
  - **Tests d’accès :**
    - Accès direct aux serveurs IIS via `http://192.168.1.10` (SRV1) et `http://192.168.1.20` (SRV2).
    - Accès au cluster NLB via `http://192.168.2.100`.
  - **Analyse des journaux :**
    - Accède aux journaux des événements sur le DC ou les serveurs pour analyser les connexions et activités.

---

### **Connexions Résumées par Réseau**

| **Réseau** | **Nom du réseau** | **Machines connectées**                                                                                     | **Fonction principale**                                               |
|------------|--------------------|-------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| **VMnet1** | Gestion & Domaine  | DC, SRV1, SRV2, CLIENT                                                                                      | Communication entre le domaine, les serveurs, et le client.           |
| **VMnet2** | Trafic NLB         | SRV1, SRV2 (avec VIP `192.168.2.100`)                                                                       | Répartition des requêtes via le cluster NLB.                          |

---

### **Validation de l’Architecture**
Avant l'examen, vous devez vérifier :
1. **Communication réseau :**
   - **VMnet1** : Toutes les machines doivent pouvoir se pinger entre elles (`ping 192.168.1.x`).
   - **VMnet2** : Les serveurs SRV1 et SRV2 doivent communiquer entre eux (`ping 192.168.2.x`).

2. **Fonctionnement du domaine :**
   - Assurez-vous que SRV1, SRV2, et CLIENT sont ajoutés au domaine `exam.local`.

3. **Accès aux services :**
   - CLIENT doit pouvoir accéder :
     - Aux pages IIS des serveurs via leurs IP individuelles (`http://192.168.1.10` et `http://192.168.1.20`).
     - À la VIP NLB via `http://192.168.2.100`.
