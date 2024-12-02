-----------------
# Partie 01 - *Architecture*
-----------------

### **Présentation**

L’examen final pratique s’appuie sur une infrastructure réseau basée sur deux réseaux virtuels (VMnet1 et VMnet2) pour simuler un environnement réaliste de gestion de domaine Active Directory et de répartition de charge réseau (NLB). 

---

### 01 - **Diagramme de l’Architecture**

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

### 02 - **Tableau des Rôles et Adresses IP**

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

### 03 - **Description des Réseaux**

1. **VMnet1 (Gestion & Domaine) :**
   - Interconnecte le **Contrôleur de Domaine (DC)**, les serveurs (SRV1 et SRV2), et le client.
   - Fournit les services DNS/DHCP nécessaires pour la gestion du domaine.

2. **VMnet2 (Trafic NLB) :**
   - Connecte uniquement les serveurs SRV1 et SRV2 pour assurer le trafic lié au cluster NLB.
   - Permet une isolation complète entre le trafic NLB et le réseau de gestion.

---

### 04 - **Détails des Composants**

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

### 05 - **Instructions pour la Préparation**

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

### **Validation**
- Avant l’examen, confirmez que les machines sont opérationnelles et que les configurations réseau sont fonctionnelles.
- Testez l’accès au domaine et au cluster NLB pour garantir le bon fonctionnement.



-----------------
# Partie 02 - *QUESTIONS*
-----------------

*Exemple de Question et de réponse:*

Dans la configuration du TP, la Virtual IP (VIP) du cluster NLB est définie comme étant `192.168.2.100` sur le réseau **VMnet2**. Expliquez pourquoi il est essentiel que cette adresse soit sur le même réseau (VMnet2) que les adresses des interfaces NLB des serveurs SRV1 et SRV2 (`192.168.2.10` et `192.168.2.20`). Que se passerait-il si la VIP était configurée sur un réseau différent, comme VMnet1 ?

**Réponse :**

- Il est important de respecter les points suivants :

1. **La VIP (192.168.2.100)** doit être configurée sur **VMnet2**, le même réseau où les serveurs SRV1 et SRV2 ont leurs interfaces NLB :
   - **SRV1 (VMnet2)** : `192.168.2.10`
   - **SRV2 (VMnet2)** : `192.168.2.20`
   - **VIP (VMnet2)** : `192.168.2.100`

2. **Les serveurs doivent avoir deux interfaces réseau :**
   - Une sur **VMnet1** (pour le domaine, gestion et administration).
   - Une sur **VMnet2** (pour le trafic NLB et la communication avec la VIP).

3. **La communication dans le cluster NLB (entre SRV1, SRV2 et la VIP) passe uniquement par VMnet2**, qui doit être correctement configuré.

Si l'énoncé respecte ces principes, il est correct. L'étudiant semble avoir mal compris la distinction entre les deux réseaux (VMnet1 pour la gestion/domaine et VMnet2 pour le trafic NLB).

---

### Ce qu'il faut vérifier dans l'énoncé pour dissiper tout doute :

1. **L'énoncé attribue correctement les adresses IP aux réseaux respectifs (VMnet1 et VMnet2)** :
   - Chaque serveur a une interface sur VMnet1 pour la gestion/domaine et une autre sur VMnet2 pour NLB.
   - La VIP est bien sur VMnet2.

2. **Les commandes et les tests demandés confirment la connectivité et la fonctionnalité :**
   - Ping entre les serveurs SRV1 et SRV2 sur leurs adresses VMnet2 (192.168.2.x).
   - Accès au cluster via la VIP (192.168.2.100).



# Exemples - **Questions et Réponses en relation avec la configuration du cluster NLB et VIP :**

# **Question 1 :**
Dans la configuration, pourquoi est-il crucial que la Virtual IP (VIP) du cluster NLB soit sur le même réseau (VMnet2) que les interfaces NLB des serveurs SRV1 et SRV2 ? Que se passerait-il si la VIP était configurée sur un autre réseau, comme VMnet1 ?

**Réponse :**
- **Pourquoi sur VMnet2 ?** 
   - Le réseau VMnet2 est exclusivement utilisé pour le trafic NLB. Les interfaces NLB de SRV1 (`192.168.2.10`) et SRV2 (`192.168.2.20`) communiquent entre elles via ce réseau pour gérer les requêtes entrantes et maintenir la synchronisation du cluster.
   - La VIP (`192.168.2.100`) représente l'adresse virtuelle utilisée pour accéder au cluster. Elle doit se situer sur le même réseau que les interfaces NLB des serveurs pour que les requêtes soient distribuées correctement.
- **Problème si la VIP est sur VMnet1 :**
   - VMnet1 est destiné à la gestion et au domaine, pas au trafic NLB. Les serveurs ne pourraient pas acheminer le trafic NLB correctement vers la VIP, entraînant une panne fonctionnelle du cluster.

---

# **Question 2 :**
Quelle est la différence entre les deux réseaux VMnet1 et VMnet2 dans cette configuration, et comment leurs rôles respectifs garantissent-ils la stabilité et la performance du cluster NLB ?

**Réponse :**
- **VMnet1 :**
   - Sert à la gestion et au domaine Active Directory (AD DS).
   - Connecte toutes les machines (DC, SRV1, SRV2, CLIENT) pour les tâches administratives, la résolution DNS, et l'intégration au domaine.
   - Les IP assignées à ce réseau sont utilisées pour accéder aux machines individuellement (`192.168.1.x`).
- **VMnet2 :**
   - Exclusivement utilisé pour le trafic NLB.
   - Connecte uniquement les serveurs SRV1 et SRV2 pour gérer les requêtes via la VIP (`192.168.2.100`).
   - Permet d'isoler le trafic de production (NLB) du trafic de gestion/domaine, assurant une meilleure performance et une gestion simplifiée.

---

# **Question 3 :**
Lors de la configuration du cluster NLB, pourquoi est-il nécessaire d’attribuer deux interfaces réseau (VMnet1 et VMnet2) aux serveurs SRV1 et SRV2 ?

**Réponse :**
- **Interface VMnet1 (Gestion/Domaine) :**
   - Utilisée pour intégrer les serveurs au domaine `exam.local`.
   - Permet de gérer les serveurs via Active Directory et de résoudre les noms DNS.
   - Facilite la communication avec le contrôleur de domaine et les autres machines sur VMnet1.
- **Interface VMnet2 (Trafic NLB) :**
   - Dédiée exclusivement au trafic NLB, assurant une séparation claire des flux réseau.
   - Permet à SRV1 et SRV2 de communiquer directement via leurs interfaces NLB (`192.168.2.10` et `192.168.2.20`) et de partager la VIP (`192.168.2.100`).
   - Garantit une meilleure performance et évite les conflits de trafic avec les tâches de gestion.

---

# **Question 4 :**
Quels tests réseau pouvez-vous effectuer pour vérifier que la configuration NLB fonctionne correctement ?

**Réponse :**
1. **Ping entre les interfaces NLB sur VMnet2 :**
   - Depuis SRV1 (`192.168.2.10`), pinger SRV2 (`192.168.2.20`) pour vérifier la connectivité directe.
   - Commande : `ping 192.168.2.20`.
2. **Ping de la VIP (192.168.2.100) depuis le CLIENT :**
   - Permet de valider que le cluster NLB est accessible via VMnet2.
   - Commande : `ping 192.168.2.100`.
3. **Test HTTP sur la VIP :**
   - Ouvrir un navigateur sur le CLIENT et accéder à `http://192.168.2.100` pour vérifier que les pages IIS des deux serveurs sont accessibles via le cluster.
4. **Simulation de panne d’un nœud :**
   - Désactiver temporairement SRV1 ou SRV2 et vérifier que l’autre serveur répond toujours aux requêtes envoyées à `http://192.168.2.100`.

---

# **Question 5 :**
Si le CLIENT ne parvient pas à accéder à la VIP (`192.168.2.100`), quelles vérifications et corrections pourriez-vous effectuer ?

**Réponse :**
1. **Vérifier la connectivité réseau :**
   - Tester si le CLIENT peut pinger les adresses IP des interfaces NLB sur VMnet2 (`192.168.2.10` et `192.168.2.20`).
   - Si le ping échoue, vérifier la configuration réseau de VMnet2 et les cartes réseau des serveurs.
2. **Vérifier la configuration du cluster NLB :**
   - S’assurer que la VIP est correctement configurée et active sur le cluster.
   - Commande : `Get-NlbCluster` pour lister les paramètres du cluster.
3. **Vérifier les services IIS :**
   - Confirmer que les services IIS sont en cours d’exécution sur SRV1 et SRV2 et que leurs pages personnalisées sont accessibles individuellement via VMnet1 (`http://192.168.1.10` et `http://192.168.1.20`).
4. **Résolution DNS :**
   - Si la VIP est configurée avec un enregistrement DNS, vérifier que le CLIENT peut résoudre le nom de la VIP.


# **Question 6 :**
Expliquez la différence entre une **Virtual IP (VIP)** et une **adresse IP physique**. Pourquoi utilise-t-on une VIP dans un cluster NLB ?

**Réponse :**
- **Adresse IP physique :**
  - C'est l'adresse attribuée à une interface réseau physique ou virtuelle sur un serveur spécifique.
  - Exemple : SRV1 sur VMnet2 a l’adresse physique `192.168.2.10`.
- **VIP (Virtual IP) :**
  - C'est une adresse partagée entre plusieurs serveurs dans un cluster, utilisée pour distribuer les requêtes entrantes de manière transparente.
  - La VIP n’est pas liée directement à une carte réseau physique mais est gérée par le cluster NLB.
  - Exemple : La VIP `192.168.2.100` est partagée entre SRV1 et SRV2.

**Pourquoi une VIP dans NLB ?**
- Permet de présenter une seule adresse IP pour le service, même si plusieurs serveurs fonctionnent en arrière-plan.
- Assure la haute disponibilité : si un serveur tombe en panne, les autres continuent de répondre via la même VIP.



# **Question 7 :**
Que se passe-t-il si deux serveurs dans un cluster NLB revendiquent simultanément la VIP sans une configuration correcte ? Quels problèmes cela pourrait-il engendrer ?

**Réponse :**
- Si deux serveurs revendiquent la VIP simultanément (sans coordination via NLB) :
  - **Conflit d’IP :** Le réseau détectera deux machines avec la même adresse IP, ce qui entraîne une instabilité et des erreurs de communication.
  - **Perte de requêtes :** Les clients peuvent recevoir des réponses incohérentes ou ne pas être servis du tout.
  - **Risque de panne généralisée :** D’autres services réseau dépendant de la VIP pourraient être impactés.



# **Question 8 :**
Expliquez le rôle d’une **Internet Gateway (IGW)** dans un réseau cloud et comment il diffère d’une VIP utilisée dans un réseau NLB.

**Réponse :**
- **Internet Gateway (IGW) :**
  - Permet aux ressources d’un réseau privé (comme un VPC dans le cloud) de communiquer avec l’Internet public.
  - Fonctionne comme une passerelle entre les adresses IP publiques et privées.
  - Exemple : Une machine dans un VPC avec une adresse privée peut utiliser l’IGW pour accéder à l’Internet.

- **VIP (Virtual IP) :**
  - Utilisée pour répartir les requêtes entre plusieurs serveurs dans un réseau local ou étendu.
  - Ne gère pas directement l’accès à l’Internet mais coordonne les requêtes entrantes vers les nœuds d’un cluster.

**Différences principales :**
| **Aspect**           | **IGW**                             | **VIP**                             |
|-----------------------|--------------------------------------|--------------------------------------|
| **Fonction principale** | Connecter un réseau privé à Internet | Répartir les requêtes au sein d’un cluster |
| **Utilisation**        | Réseaux cloud ou hybrides           | Réseaux locaux ou répartis          |
| **Communication**      | Entre réseau privé et public        | Entre clients et serveurs d’un cluster |



# **Question 9 :**
Dans un cluster NLB, que se passe-t-il si un serveur tombe en panne ? Comment la VIP continue-t-elle de fonctionner ?

**Réponse :**
- **Si un serveur tombe en panne :**
  - Le cluster NLB détecte l’indisponibilité du serveur défaillant.
  - La VIP reste active sur les serveurs restants, qui se partagent désormais toutes les requêtes.

**Pourquoi la VIP fonctionne toujours ?**
- La VIP n'est pas liée à un serveur individuel mais au cluster dans son ensemble. Tant qu'un ou plusieurs serveurs dans le cluster fonctionnent, la VIP reste disponible.



# **Question 10 :**
Quels avantages présente la séparation des réseaux (gestion et NLB) dans une architecture réseau ? Que pourrait-il se passer si tout le trafic passait par un seul réseau ?

**Réponse :**
- **Avantages de la séparation des réseaux :**
  - **Isolation des flux :** Le trafic administratif et celui lié au service (NLB) ne se chevauchent pas, évitant les conflits et améliorant la performance.
  - **Sécurité :** Les données sensibles du domaine (gestion) sont protégées des intrusions sur le réseau de production.
  - **Performance accrue :** Le réseau de gestion (VMnet1) reste fluide même en cas de forte charge sur le réseau NLB (VMnet2).

- **Risques d'un seul réseau :**
  - **Conflits de trafic :** Les requêtes NLB pourraient saturer le réseau, rendant difficile la gestion des serveurs et du domaine.
  - **Risque de panne généralisée :** Une surcharge sur le réseau unique pourrait affecter à la fois la gestion et la production.



# **Question 11 :**
Expliquez comment un client accède à un service sur un cluster NLB. Décrivez le rôle du DNS dans ce processus.

**Réponse :**
1. **Requête du client :**
   - Le client envoie une requête à la VIP (`192.168.2.100` ou un nom DNS comme `service.exam.local`).

2. **Rôle du DNS :**
   - Si un nom DNS est utilisé, le client interroge le serveur DNS (ex : DC) pour obtenir l'adresse IP associée au nom.
   - Exemple : `service.exam.local` → `192.168.2.100`.

3. **Routage de la requête :**
   - La requête est dirigée vers le cluster NLB via la VIP.
   - Le cluster choisit un serveur disponible (SRV1 ou SRV2) pour traiter la requête.

4. **Réponse :**
   - Le serveur sélectionné répond directement au client.



# **Question 12 :**
Pourquoi est-il important que les serveurs du cluster NLB synchronisent leurs sessions ? Quelles conséquences en cas d’absence de synchronisation ?

**Réponse :**
- **Importance de la synchronisation :**
  - La synchronisation garantit que toutes les requêtes d’un client dans une même session sont servies par le même serveur.
  - Exemple : Un utilisateur accédant à une application web reste connecté au même serveur pour éviter des erreurs ou des incohérences.

- **Conséquences de l’absence de synchronisation :**
  - **Sessions interrompues :** Les requêtes successives d’un client pourraient être traitées par différents serveurs, causant des erreurs de connexion.
  - **Incohérences :** Des données temporaires ou des paramètres de session pourraient être perdus.
  - **Mauvaise expérience utilisateur :** L’utilisateur pourrait être obligé de se reconnecter ou de recommencer ses actions.


