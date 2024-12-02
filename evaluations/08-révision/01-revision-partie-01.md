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





---

# Partie 03 -  **Quiz sur les concepts liés à IGW, Clusters, VIP, et VMnet**

---

#### **Section 1: Concepts de base (IGW, VIP, et NLB)**

1. **Quel est le rôle d’une Internet Gateway (IGW) dans un réseau cloud ?**  
   a) Répartir les requêtes entre plusieurs serveurs  
   b) Fournir une connexion Internet à un réseau privé  
   c) Créer un domaine Active Directory  
   d) Définir des adresses IP statiques  

   **Réponse : b**

2. **La Virtual IP (VIP) dans un cluster NLB est utilisée pour :**  
   a) Identifier chaque serveur individuellement  
   b) Servir de passerelle Internet pour le réseau  
   c) Présenter une seule adresse IP pour l'ensemble des serveurs du cluster  
   d) Configurer les adresses MAC des interfaces réseau  

   **Réponse : c**

3. **Dans un cluster NLB, la VIP doit être configurée :**  
   a) Sur un réseau distinct de celui des serveurs du cluster  
   b) Sur le même réseau que les interfaces réseau NLB des serveurs  
   c) Sur le serveur DNS uniquement  
   d) Sur le client qui teste l’accès au cluster  

   **Réponse : b**

4. **Que se passe-t-il si un serveur du cluster NLB tombe en panne ?**  
   a) Le cluster cesse complètement de fonctionner  
   b) La VIP est automatiquement désactivée  
   c) Les autres serveurs prennent en charge les requêtes via la VIP  
   d) Le réseau bascule sur un IGW secondaire  

   **Réponse : c**

5. **Pourquoi utilise-t-on une VIP au lieu d’une adresse IP physique dans un cluster ?**  
   a) Pour permettre une haute disponibilité et un basculement transparent  
   b) Pour réduire les conflits d’adresses IP  
   c) Pour configurer les adresses MAC automatiquement  
   d) Pour accéder directement à Internet via l'IGW  

   **Réponse : a**

---

#### **Section 2: Configuration et choix des réseaux VMnet**

6. **Quel est l’avantage principal d’utiliser plusieurs réseaux (VMnets) dans une architecture réseau ?**  
   a) Réduire la consommation de bande passante  
   b) Séparer les différents types de trafic pour éviter les conflits  
   c) Rendre les adresses IP statiques inutiles  
   d) Simplifier la configuration des machines virtuelles  

   **Réponse : b**

7. **Dans l’architecture donnée, VMnet1 est utilisé pour :**  
   a) Le trafic NLB uniquement  
   b) La gestion et le domaine Active Directory  
   c) Les communications entre les serveurs NLB uniquement  
   d) Connecter les machines au réseau Internet  

   **Réponse : b**

8. **VMnet2 est configuré pour :**  
   a) Le domaine Active Directory  
   b) Le trafic NLB et la communication entre les nœuds du cluster  
   c) Fournir des services DNS aux serveurs NLB  
   d) Héberger l’Internet Gateway (IGW)  

   **Réponse : b**

9. **Quel problème pourrait survenir si le trafic de gestion et NLB partageait un seul réseau VMnet ?**  
   a) Les adresses IP des serveurs seraient mal configurées  
   b) Le réseau pourrait être saturé, impactant les performances du domaine et du NLB  
   c) Le cluster NLB ne fonctionnerait pas sans une IGW supplémentaire  
   d) La VIP ne pourrait pas être activée  

   **Réponse : b**

10. **Pour une architecture incluant un NLB et un domaine, combien de réseaux VMnet sont recommandés au minimum ?**  
    a) Un seul  
    b) Deux : un pour la gestion et un pour le trafic NLB  
    c) Trois : un pour la gestion, un pour le domaine, et un pour le trafic NLB  
    d) Quatre : un par machine virtuelle  

    **Réponse : b**

---

#### **Section 3: Active Directory et domaines**

11. **Quelle machine fournit les services DNS et gère les utilisateurs dans un domaine Active Directory ?**  
    a) Un serveur NLB  
    b) Le contrôleur de domaine (DC)  
    c) Une machine cliente  
    d) L’Internet Gateway (IGW)  

    **Réponse : b**

12. **Dans un domaine Active Directory, pourquoi est-il nécessaire que toutes les machines soient connectées au même réseau (VMnet1 dans l’exemple) ?**  
    a) Pour garantir une résolution DNS et une gestion centralisée des membres du domaine  
    b) Pour synchroniser les configurations NLB  
    c) Pour activer la VIP sur toutes les machines  
    d) Pour permettre un accès direct à Internet  

    **Réponse : a**

13. **Que se passe-t-il si une machine cliente n’est pas membre du domaine Active Directory ?**  
    a) Elle ne pourra pas accéder au cluster NLB  
    b) Elle ne pourra pas utiliser les services gérés par le DC, comme le DNS  
    c) Elle ne pourra pas utiliser une VIP  
    d) Elle pourra fonctionner normalement, sans aucune restriction  

    **Réponse : b**

---

#### **Section 4: Concepts avancés**

14. **Une VIP peut-elle exister dans un réseau cloud sans un cluster NLB ?**  
    a) Oui, elle peut être utilisée indépendamment pour d'autres services  
    b) Non, elle est spécifique aux clusters NLB  
    c) Oui, mais uniquement dans un réseau local (LAN)  
    d) Non, elle nécessite toujours une IGW  

    **Réponse : a**

15. **Pourquoi une IGW n’est-elle pas nécessaire dans une architecture NLB locale ?**  
    a) Parce que la communication reste interne au réseau local  
    b) Parce que la VIP remplace le rôle de l’IGW  
    c) Parce qu’un IGW est réservé aux environnements cloud  
    d) Parce que le contrôleur de domaine remplit déjà ce rôle  

    **Réponse : a**

16. **Dans un environnement NLB, que signifie le basculement (failover) ?**  
    a) Une nouvelle VIP est générée si un serveur tombe en panne  
    b) Les requêtes sont automatiquement redirigées vers un autre serveur fonctionnel  
    c) Une IGW est activée pour gérer le trafic supplémentaire  
    d) Le domaine Active Directory est suspendu temporairement  

    **Réponse : b**

17. **Que faut-il vérifier pour garantir que la VIP fonctionne correctement ?**  
    a) La synchronisation des adresses IP entre les interfaces NLB et DNS  
    b) Que toutes les interfaces NLB des serveurs sont sur le même réseau  
    c) Que le client est connecté directement à VMnet2  
    d) Que le DNS pointe vers une IGW configurée  

    **Réponse : b**

---

#### **Section 5: Questions scénarios**

18. **Scénario :**  
   Un étudiant configure SRV1 avec `192.168.1.10` sur VMnet1 et `192.168.1.11` sur VMnet2. Que se passera-t-il avec le cluster NLB ?  
   a) Le cluster fonctionnera normalement  
   b) Le cluster échouera car les adresses sur VMnet2 doivent être dans le réseau `192.168.2.x`  
   c) Le domaine sera affecté par la configuration NLB  
   d) Le client ne pourra pas accéder au cluster NLB  

   **Réponse : b**

19. **Scénario :**  
   Le client peut accéder à la VIP (`192.168.2.100`) mais reçoit des erreurs aléatoires. Quelle pourrait être la cause ?  
   a) La synchronisation des sessions n’est pas activée dans le cluster NLB  
   b) L’adresse VIP n’est pas configurée dans le DNS  
   c) Le client n’est pas membre du domaine  
   d) L’IGW n’est pas active  

   **Réponse : a**



------

### **Section 6: Concepts Avancés et Scénarios Pratiques**  
*(Questions 20 à 50)*  

---

#### **20.**  
Pourquoi est-il recommandé d’attribuer des adresses IP fixes aux interfaces réseau des serveurs dans un cluster NLB ?  
a) Pour éviter les conflits d'adresses lors de l’attribution par DHCP  
b) Pour permettre une résolution DNS rapide  
c) Pour garantir la cohérence des communications au sein du cluster  
d) Toutes ces réponses  

**Réponse : d**

---

#### **21.**  
Dans un cluster NLB, quelle commande PowerShell est utilisée pour ajouter un nouveau nœud ?  
a) `Add-NlbClusterNode`  
b) `Set-NlbClusterVip`  
c) `New-NlbClusterNode`  
d) `Configure-NlbClusterNode`  

**Réponse : a**

---

#### **22.**  
Pourquoi faut-il configurer une interface séparée pour le trafic NLB (VMnet2) ?  
a) Pour améliorer les performances du cluster  
b) Pour isoler le trafic NLB du réseau de gestion  
c) Pour éviter les saturations sur le réseau VMnet1  
d) Toutes ces réponses  

**Réponse : d**

---

#### **23.**  
Dans un domaine Active Directory, quelle est la fonction principale du DNS ?  
a) Gérer les requêtes HTTP  
b) Résoudre les noms des machines et services au sein du domaine  
c) Attribuer des adresses IP aux interfaces réseau  
d) Configurer les clusters NLB  

**Réponse : b**

---

#### **24.**  
Que se passe-t-il si une VIP est configurée avec une adresse IP en dehors du réseau VMnet2 ?  
a) Le cluster NLB fonctionnera sans problème  
b) Les serveurs ne pourront pas communiquer avec la VIP  
c) La VIP sera automatiquement réattribuée  
d) Le domaine Active Directory cessera de fonctionner  

**Réponse : b**

---

#### **25.**  
Lorsqu’un client envoie une requête à un cluster NLB, comment le serveur choisi est-il déterminé ?  
a) Par un algorithme basé sur l’adresse IP source du client  
b) Par le serveur DNS du domaine  
c) Par l’ordre d’enregistrement des serveurs dans le cluster  
d) Par l’adresse IP de l’Internet Gateway  

**Réponse : a**

---

#### **26.**  
Quels sont les avantages de configurer une synchronisation de session dans un cluster NLB ?  
a) Maintenir la cohérence des données pour les utilisateurs  
b) Répartir les sessions de manière égale entre tous les serveurs  
c) Augmenter la vitesse des requêtes  
d) Éliminer complètement les pannes  

**Réponse : a**

---

#### **27.**  
Quel test effectuer pour vérifier que les serveurs NLB partagent correctement la VIP ?  
a) `ping 192.168.2.100` depuis le CLIENT  
b) Accéder à `http://192.168.2.100` via un navigateur sur le CLIENT  
c) Simuler une panne d’un serveur et vérifier l’accès à la VIP  
d) Toutes ces réponses  

**Réponse : d**

---

#### **28.**  
Pourquoi la configuration d’une VIP est-elle une bonne pratique pour la haute disponibilité ?  
a) Elle permet de basculer automatiquement les requêtes en cas de panne d’un nœud  
b) Elle simplifie la configuration des clients  
c) Elle garantit une performance constante du réseau  
d) Toutes ces réponses  

**Réponse : d**

---

#### **29.**  
Quelle commande PowerShell peut être utilisée pour vérifier la configuration d’un cluster NLB existant ?  
a) `Get-NlbCluster`  
b) `Check-NlbCluster`  
c) `Test-NlbCluster`  
d) `Inspect-NlbCluster`  

**Réponse : a**

---

#### **30.**  
Si le client n’arrive pas à résoudre `exam.local`, quelle serait une cause probable ?  
a) Le contrôleur de domaine (DC) est hors ligne  
b) Le client n’est pas configuré pour utiliser le serveur DNS  
c) Une erreur dans les enregistrements DNS  
d) Toutes ces réponses  

**Réponse : d**

---

#### **31.**  
Dans une architecture réseau avec plusieurs VMnets, comment s’assurer que les serveurs peuvent communiquer entre eux ?  
a) Configurer les adresses IP sur le même sous-réseau pour chaque VMnet  
b) Activer les services DHCP pour attribuer automatiquement des IP  
c) Configurer une passerelle entre les VMnets  
d) Ajouter les interfaces réseau au même domaine Active Directory  

**Réponse : a**

---

#### **32.**  
Dans un cluster NLB, pourquoi est-il important de tester l’accès direct à chaque serveur (SRV1, SRV2) avant de tester la VIP ?  
a) Pour vérifier que les services individuels fonctionnent correctement  
b) Pour s’assurer que les interfaces réseau sont actives  
c) Pour identifier rapidement les problèmes liés à un serveur spécifique  
d) Toutes ces réponses  

**Réponse : d**

---

#### **33.**  
Que représente l’adresse IP `192.168.1.5` dans l’architecture réseau donnée ?  
a) La VIP du cluster NLB  
b) L’adresse IP du contrôleur de domaine (DC)  
c) L’adresse du client  
d) L’adresse du serveur DNS secondaire  

**Réponse : b**

---

#### **34.**  
Quels outils peuvent être utilisés pour analyser le trafic réseau entre les serveurs NLB ?  
a) Wireshark  
b) Microsoft Message Analyzer  
c) Commande `ping`  
d) Toutes ces réponses  

**Réponse : d**

---

#### **35.**  
Que signifie "basculement" (failover) dans un cluster NLB ?  
a) Le remplacement automatique de la VIP en cas de panne  
b) La redirection des requêtes vers un autre serveur disponible  
c) La création d’une nouvelle session pour chaque client  
d) La désactivation temporaire du domaine Active Directory  

**Réponse : b**

---

#### **36.**  
Comment tester qu’un serveur SRV1 a bien rejoint le domaine Active Directory `exam.local` ?  
a) En accédant à la page IIS de SRV1 via la VIP  
b) En vérifiant que SRV1 peut résoudre le nom `exam.local`  
c) En effectuant un ping vers `192.168.2.100` depuis SRV1  
d) En accédant à `http://192.168.1.10` depuis le CLIENT  

**Réponse : b**

---

#### **37.**  
Quel est le rôle de la VIP dans un cluster NLB ?  
a) Fournir un accès direct à chaque serveur du cluster  
b) Présenter une adresse unique pour distribuer les requêtes entre les serveurs  
c) Remplacer les adresses IP des serveurs en cas de panne  
d) Synchroniser les sessions entre les serveurs  

**Réponse : b**

---

#### **38.**  
Pourquoi est-il recommandé d’utiliser deux interfaces réseau sur chaque serveur NLB (gestion et trafic NLB) ?  
a) Pour éviter les conflits de trafic entre les tâches de gestion et de production  
b) Pour améliorer la sécurité des données  
c) Pour simplifier la configuration des VIP  
d) Toutes ces réponses  

**Réponse : d**

---

#### **39.**  
Que se passe-t-il si un serveur NLB est configuré avec une adresse IP incorrecte sur VMnet2 ?  
a) Il sera exclu du cluster  
b) Il continuera à fonctionner uniquement sur VMnet1  
c) La VIP sera automatiquement désactivée  
d) Toutes les requêtes du client échoueront  

**Réponse : a**

---

#### **40.**  
Dans un environnement NLB, que signifie "affinité" dans le contexte de la gestion des sessions ?  
a) La synchronisation des requêtes entre les serveurs  
b) La redirection des requêtes successives d’un client vers le même serveur  
c) L’attribution d’une nouvelle session à chaque requête  
d) La configuration des interfaces réseau sur le même sous-réseau  

**Réponse : b**



#### **41.**  
Quel est le rôle principal d’un contrôleur de domaine (DC) dans une infrastructure réseau ?  
a) Fournir des services web via IIS  
b) Gérer les utilisateurs, groupes et machines dans un domaine Active Directory  
c) Assurer le basculement dans un cluster NLB  
d) Attribuer des adresses IP aux machines  

**Réponse : b**

---

#### **42.**  
Dans une configuration NLB, que signifie le mode "Multicast" ?  
a) Le trafic NLB est distribué à tous les serveurs via une adresse MAC partagée  
b) Les requêtes des clients sont acheminées vers un seul serveur disponible  
c) Chaque serveur reçoit une copie des requêtes et les traite simultanément  
d) La VIP est partagée entre deux réseaux différents  

**Réponse : a**

---

#### **43.**  
Pourquoi est-il important de vérifier la résolution DNS pour la VIP dans un cluster NLB ?  
a) Pour garantir que les clients puissent atteindre le cluster via un nom convivial  
b) Pour synchroniser les serveurs NLB avec le contrôleur de domaine  
c) Pour tester la configuration des interfaces NLB  
d) Pour permettre l’accès direct aux interfaces réseau des serveurs  

**Réponse : a**

---

#### **44.**  
Quel type de données peut-on analyser dans les journaux d’événements liés au cluster NLB ?  
a) Les connexions des clients à la VIP  
b) Les erreurs réseau entre les interfaces NLB  
c) Les changements dans la configuration du cluster  
d) Toutes ces réponses  

**Réponse : d**

---

#### **45.**  
Lors de l’accès à `http://192.168.2.100`, vous remarquez que les requêtes ne parviennent qu’à un seul serveur du cluster. Quelle configuration vérifier en priorité ?  
a) Le mode d’affinité du cluster NLB  
b) Les enregistrements DNS pour la VIP  
c) Les permissions des utilisateurs dans le domaine  
d) La configuration des adresses IP dans le réseau VMnet1  

**Réponse : a**

---

#### **46.**  
Dans un cluster NLB, quelle est la commande PowerShell pour activer une adresse IP virtuelle (VIP) sur un nœud spécifique ?  
a) `Set-NlbClusterVip`  
b) `Enable-NlbClusterVip`  
c) `Add-NlbClusterVip`  
d) `Configure-NlbClusterVip`  

**Réponse : c**

---

#### **47.**  
Quel problème peut survenir si deux clusters NLB partagent la même VIP ?  
a) Les clients ne pourront pas atteindre le cluster  
b) Un conflit d’adresse IP entraînera une panne réseau  
c) Les serveurs ne pourront pas synchroniser leurs sessions  
d) Les nœuds du cluster seront exclus automatiquement  

**Réponse : b**

---

#### **48.**  
Dans quel cas utiliseriez-vous un protocole de type IGMP (Internet Group Management Protocol) dans une configuration NLB ?  
a) Pour gérer les sessions synchronisées entre les serveurs  
b) Pour limiter le trafic multicast aux routeurs ou commutateurs configurés  
c) Pour basculer automatiquement entre les interfaces réseau  
d) Pour assurer la sécurité des communications NLB  

**Réponse : b**

---

#### **49.**  
Si un client accède à `http://192.168.2.100` mais reçoit des réponses incohérentes (ex. : différentes pages IIS), quelle configuration est probablement incorrecte ?  
a) La synchronisation des sessions NLB n’est pas activée  
b) Les adresses IP des interfaces NLB ne sont pas dans le même sous-réseau  
c) Les serveurs ne sont pas dans le domaine Active Directory  
d) Les permissions DNS pour le domaine sont mal configurées  

**Réponse : a**

---

#### **50.**  
Pourquoi est-il essentiel d’utiliser deux réseaux VMnet (gestion et NLB) dans l’architecture décrite ?  
a) Pour éviter la saturation d’un réseau unique  
b) Pour séparer les tâches administratives et le trafic NLB  
c) Pour simplifier la gestion des configurations IP et VIP  
d) Toutes ces réponses  

**Réponse : d**

---

# **Résumé des Thématiques**  

Ces 50 questions couvrent :  
1. Les concepts fondamentaux (VIP, NLB, IGW).  
2. Les configurations spécifiques (réseaux VMnet, DNS, gestion des sessions).  
3. Les scénarios pratiques (dépannage, basculement, connectivité).  
4. Les outils et commandes nécessaires pour tester et analyser l’infrastructure.  

