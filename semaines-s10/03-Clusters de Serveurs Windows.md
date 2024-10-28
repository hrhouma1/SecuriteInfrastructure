# **3. Clusters de Serveurs Windows**

# Introduction :

Les clusters de serveurs Windows sont essentiels pour améliorer la disponibilité et la performance des services dans un environnement informatique. Ils permettent de répartir la charge entre plusieurs serveurs ou d'assurer la continuité des services en cas de panne. Ce cours aborde trois techniques de clustering : **Distribution Round-robin (RR)**, **Network Load Balancing (NLB)**, et **Failover Cluster**.

---

#### **3.1 Distribution Round-robin (RR)**

**Définition** : La distribution Round-robin (RR) est une technique de répartition de charge simple où chaque requête est distribuée successivement aux serveurs disponibles, dans un ordre circulaire.

**Fonctionnement** :
- Avec RR, les requêtes sont distribuées de manière séquentielle : la première requête va au premier serveur, la seconde au second, et ainsi de suite, jusqu'à revenir au premier serveur une fois que tous les autres ont reçu une requête.
- C'est une méthode de répartition sans état : chaque requête est traitée indépendamment des précédentes.
- Utilisé généralement pour des services réseau comme le DHCP (Dynamic Host Configuration Protocol) ou le DNS (Domain Name System).

**Avantages** :
- **Simplicité** : RR est facile à configurer et à comprendre.
- **Équilibrage basique** : Il assure une répartition équitable des requêtes entre les serveurs.
- **Compatibilité** : Fonctionne avec de nombreux types de services, même ceux qui ne nécessitent pas de gestion d'état.

**Inconvénients** :
- **Absence de prise en compte de la charge** : RR ne tient pas compte de la charge actuelle des serveurs ; si un serveur est plus lent, il recevra tout de même des requêtes de manière équitable.
- **Pas d’affinité de session** : Ce type de distribution ne garantit pas que les utilisateurs seront toujours dirigés vers le même serveur.

---

##### **Exemple Pratique : Configurer RR sur un Serveur DHCP**

La configuration de la distribution Round-robin sur un serveur DHCP permet de distribuer les requêtes DHCP de manière circulaire entre plusieurs serveurs, assurant ainsi une répartition de charge basique.

**Étapes pour Configurer RR sur un Serveur DHCP :**
1. **Installer le rôle DHCP** sur chaque serveur Windows qui participera à la répartition de charge.
   - Ouvrez le Gestionnaire de serveur, allez dans **Ajouter des rôles et fonctionnalités** et sélectionnez **DHCP Server**.
2. **Configurer le DHCP avec des plages d'adresses IP** uniques sur chaque serveur :
   - Assignez des plages d’adresses IP différentes à chaque serveur pour éviter les conflits d’adresses IP.
   - Par exemple, le serveur A pourrait gérer les adresses de 192.168.1.1 à 192.168.1.100, et le serveur B de 192.168.1.101 à 192.168.1.200.
3. **Configurer le DNS pour le Round-robin** :
   - Dans la console DNS, créez un enregistrement DNS pour le service DHCP avec plusieurs entrées pointant vers chaque serveur DHCP.
   - Par exemple, un enregistrement `dhcp.local` peut pointer vers les IP des deux serveurs : 192.168.1.10 (serveur A) et 192.168.1.20 (serveur B).
4. **Tester la configuration** :
   - Effectuez une requête DHCP et vérifiez que les clients reçoivent des réponses de chaque serveur DHCP de manière circulaire.

---

#### **3.2 Réplication de Charge Réseau (NLB - Network Load Balancing)**

**Définition** : Le Network Load Balancing (NLB) est une technique de clustering utilisée pour répartir les requêtes réseau entre plusieurs serveurs, améliorant ainsi la performance et la disponibilité des applications.

**Fonctionnement** :
- Avec NLB, plusieurs serveurs partagent la même adresse IP virtuelle. Le trafic est distribué entre eux en fonction de la charge réseau.
- Chaque serveur NLB reçoit une portion des requêtes, augmentant ainsi la capacité totale du système et réduisant les risques de surcharge.
- NLB est couramment utilisé pour des applications Web, des services de messagerie, ou toute application qui nécessite une haute disponibilité.

**Avantages** :
- **Amélioration de la performance** : NLB distribue les requêtes, réduisant la charge individuelle sur chaque serveur.
- **Tolérance aux pannes** : En cas de panne d'un serveur, les autres serveurs continuent de traiter les requêtes.
- **Scalabilité** : Permet d'ajouter des serveurs supplémentaires au cluster sans affecter les utilisateurs.

**Inconvénients** :
- **Complexité de la configuration** : La configuration de NLB peut être complexe et nécessite une bonne planification.
- **Gestion de session limitée** : NLB ne garantit pas que les utilisateurs soient dirigés vers le même serveur à chaque connexion, sauf si une affinité de session est configurée.

---

##### **Cas Pratique : Configurer NLB pour des Serveurs Web sous Windows Server**

Configurer NLB sur des serveurs Web permet de répartir les requêtes entrantes sur plusieurs serveurs, améliorant ainsi la performance et la tolérance aux pannes.

**Étapes pour Configurer NLB :**
1. **Préparer les serveurs** :
   - Assurez-vous que chaque serveur dispose de Windows Server installé et que les rôles nécessaires sont en place (ex. : IIS pour les serveurs Web).
2. **Installer la fonctionnalité NLB** :
   - Allez dans le Gestionnaire de serveur, sélectionnez **Ajouter des rôles et fonctionnalités**, puis activez **Network Load Balancing**.
3. **Créer un Cluster NLB** :
   - Ouvrez la console NLB et sélectionnez **Créer un nouveau cluster**.
   - Entrez l’adresse IP virtuelle que vous souhaitez utiliser pour le cluster. Cette IP sera celle que les utilisateurs verront et utiliseront pour accéder aux serveurs.
4. **Ajouter les hôtes au cluster** :
   - Ajoutez chaque serveur au cluster et configurez leurs priorités. Le serveur avec la priorité la plus élevée sera le serveur principal.
5. **Configurer l’affinité (optionnel)** :
   - Pour les applications nécessitant une affinité de session (comme les sessions de connexion persistantes), définissez le type d'affinité (par IP par exemple) dans les paramètres NLB.
6. **Vérifier le bon fonctionnement** :
   - Testez la configuration en accédant à l’adresse IP virtuelle et en vérifiant que les requêtes sont distribuées entre les serveurs.

**Exemple** : Un site web d'une entreprise est configuré en NLB avec trois serveurs Web (192.168.1.11, 192.168.1.12, et 192.168.1.13) sous une IP virtuelle commune (192.168.1.10). Le trafic utilisateur est automatiquement réparti entre ces serveurs.

---

#### **3.3 Cluster de Basculement (Failover Cluster)**

**Définition** : Le Failover Cluster est une solution de haute disponibilité qui permet de basculer les services vers un serveur secondaire en cas de panne du serveur principal.

**Fonctionnement** :
- Dans un cluster de basculement, plusieurs serveurs sont configurés pour fournir un service. L'un des serveurs est actif et les autres sont en veille.
- Si le serveur actif tombe en panne, un serveur de secours prend automatiquement le relais et devient le nouveau serveur actif.
- Utilisé pour des applications critiques, comme les bases de données, les applications métiers et les services réseau (DHCP, SQL Server).

**Avantages** :
- **Haute disponibilité** : Garantit que le service reste disponible même en cas de panne matérielle ou logicielle.
- **Tolérance aux pannes** : Redondance des services pour éviter l'interruption de service.
- **Gestion simplifiée des mises à jour** : Permet de déplacer temporairement le service vers un autre nœud pour effectuer des mises à jour ou des réparations sans interruption.

**Inconvénients** :
- **Coût élevé** : Exige souvent du matériel et des licences supplémentaires pour garantir la redondance.
- **Complexité de la configuration et de la gestion** : La configuration et la maintenance d'un cluster de basculement demandent des compétences avancées.

---

##### **Exemple Pratique : Créer un Cluster de Basculement sur un Serveur Windows avec un Serveur DHCP**

La configuration d'un cluster de basculement pour un serveur DHCP permet d’assurer que le service DHCP reste disponible en cas de panne.

**Étapes pour Configurer un Cluster de Basculement :**
1. **Préparer les serveurs** :
   - Assurez-vous d’avoir au moins deux serveurs avec le rôle DHCP installé.
   - Connectez les serveurs à un stockage partagé (par exemple, un SAN ou un NAS) pour stocker les configurations DHCP et les données de cluster.
2. **Installer la fonctionnalité de Cluster de Basculement** :
   - Allez dans le Gestionnaire de serveur, sélectionnez **Ajouter des rôles et fonctionnalités**, puis activez **Failover Clustering**.
3. **Configurer le Cluster de Basculement** :
  

 - Ouvrez la console de gestion de cluster de basculement et sélectionnez **Créer un cluster**.
   - Ajoutez les serveurs au cluster, puis configurez les paramètres de basculement pour que le service DHCP puisse être déplacé automatiquement en cas de panne.
4. **Tester le basculement** :
   - Désactivez temporairement le serveur actif et vérifiez que le serveur secondaire prend automatiquement le relais en devenant le serveur DHCP principal.
5. **Surveillance et Gestion du Cluster** :
   - Configurez des alertes et surveillez le cluster pour détecter les pannes et basculements éventuels.
   - Utilisez la console de gestion de cluster pour vérifier l'état des nœuds et effectuer les tâches de maintenance.

**Exemple** : Une entreprise configure un cluster de basculement pour ses serveurs DHCP sur deux serveurs (192.168.1.21 et 192.168.1.22). En cas de panne du serveur primaire, le serveur secondaire prend automatiquement le relais pour fournir les adresses IP aux clients.

---

### **Résumé des Techniques de Clustering de Serveurs Windows**

| Technique                | Description                                     | Avantages                               | Inconvénients                             | Exemple Pratique                          |
|--------------------------|-------------------------------------------------|-----------------------------------------|-------------------------------------------|-------------------------------------------|
| Distribution Round-robin | Répartition simple des requêtes entre serveurs  | Facile à configurer                     | Pas d'affinité de session, ignore la charge | Configurer RR pour des serveurs DHCP      |
| Network Load Balancing   | Répartition de la charge réseau entre serveurs  | Amélioration de la performance          | Complexité de configuration, affinité limitée | Configurer NLB pour des serveurs Web      |
| Failover Cluster         | Basculement en cas de panne                     | Haute disponibilité, tolérance aux pannes | Coût et complexité élevés                 | Cluster de basculement pour serveur DHCP  |

---

### **Conclusion**

Les clusters de serveurs Windows offrent des solutions efficaces pour la répartition de charge et la haute disponibilité. La **distribution Round-robin** est idéale pour les besoins simples de répartition, tandis que le **Network Load Balancing** est adapté aux applications nécessitant une répartition de charge dynamique. Enfin, le **Failover Cluster** est la solution de choix pour les applications critiques nécessitant une continuité de service en cas de panne.

Ces solutions de clustering sont essentielles pour les entreprises cherchant à garantir une disponibilité et une performance optimales de leurs services.
