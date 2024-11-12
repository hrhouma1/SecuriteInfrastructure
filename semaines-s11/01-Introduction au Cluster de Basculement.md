Voici un cours sur le **Cluster de Basculement (Failover Cluster)**, structuré en plusieurs sections pour aborder les concepts de base, l'installation, et la gestion. Ce cours est conçu pour vous donner une compréhension complète et pratique du sujet.

---

### 🚀 Introduction au Cluster de Basculement

Un **Cluster de Basculement** est un ensemble de serveurs ou de nœuds qui fonctionnent ensemble pour garantir la disponibilité des services en cas de panne matérielle ou logicielle. Si l'un des serveurs rencontre un problème, un autre prend automatiquement la relève, minimisant ainsi les interruptions de service.

#### Pourquoi utiliser un Cluster de Basculement ?
- **Haute disponibilité** : Assure que les services sont constamment en ligne.
- **Tolérance aux pannes** : Permet de gérer les défaillances matérielles ou logicielles sans perturber les utilisateurs.
- **Amélioration des performances** : La charge peut être distribuée entre plusieurs nœuds.

#### Concepts clés
- **Nœud** : Un serveur participant au cluster.
- **Ressources** : Les services et applications que le cluster doit gérer.
- **Quorum** : Mécanisme qui détermine si le cluster est opérationnel, même en cas de panne de certains nœuds.
- **Failover** : Processus de transfert automatique des services vers un autre nœud en cas de panne.

---

### 🖥️ Configuration et Installation d’un Cluster de Basculement sous Windows Server

1. **Pré-requis matériels et logiciels**
   - Deux serveurs minimum avec Windows Server (2016 ou plus récent).
   - Un réseau commun pour connecter les nœuds.
   - Un système de stockage partagé accessible par les nœuds (par exemple, via SAN).

2. **Préparer l'environnement de réseau**
   - Assurez-vous que chaque nœud peut communiquer avec les autres.
   - Assignez des adresses IP fixes pour chaque serveur.
   - Configurez un DNS pour tous les nœuds.

3. **Installation du rôle Cluster de Basculement**
   - Accédez au **Gestionnaire de Serveur** et cliquez sur **Ajouter des rôles et fonctionnalités**.
   - Sélectionnez **Cluster de Basculement** sous les fonctionnalités.
   - Installez cette fonctionnalité sur tous les nœuds participants.

4. **Validation du cluster**
   - Utilisez l’outil **Validation du Cluster** pour vérifier que votre environnement répond aux critères pour configurer un cluster.
   - Lancez le **Gestionnaire de Cluster de Basculement**, puis sélectionnez **Valider la configuration**.
   - L’assistant effectuera une série de tests et vous informera des éventuelles erreurs à corriger.

5. **Création du Cluster de Basculement**
   - Une fois la validation réussie, utilisez l'**Assistant de Création de Cluster** dans le Gestionnaire de Cluster.
   - Entrez les noms des serveurs/nœuds et définissez un nom pour le cluster.
   - Spécifiez les ressources partagées et les configurations réseau.

---

### 🔄 Gestion des Ressources et Basculement (Failover)

1. **Configuration du Quorum**
   - Le quorum peut être configuré en fonction du nombre de nœuds et de l’environnement.
   - **Types de quorum** :
     - **Disk Witness** : Utilise un disque partagé.
     - **File Share Witness** : Utilise un partage réseau.
     - **Node Majority** : Basé sur le nombre de nœuds.

2. **Configuration des applications et services dans le cluster**
   - Ajoutez les services ou applications que vous souhaitez rendre disponibles dans le cluster.
   - Exemple : ajouter un service SQL Server pour qu’il bascule en cas de panne.

3. **Gestion du basculement (Failover)**
   - Le processus de **Failover** se fait automatiquement lors d’une panne détectée par le cluster.
   - Vous pouvez également forcer le basculement manuellement depuis le Gestionnaire de Cluster.

4. **Paramétrer le retour automatique (Failback)**
   - Configurez les paramètres pour permettre à une ressource de revenir automatiquement sur son nœud d’origine une fois le problème résolu.

---

### 🔍 Surveillance et Maintenance du Cluster

1. **Surveillance des événements de basculement**
   - Utilisez l’Observateur d’événements pour surveiller les logs de basculement.
   - Configurez des alertes pour être notifié en cas de panne ou de basculement.

2. **Maintenance et mise à jour**
   - **Mode de maintenance** : Lors de mises à jour, mettez temporairement les nœuds en mode de maintenance pour éviter des basculements non voulus.
   - **Windows Update** : Mettez à jour chaque nœud individuellement en mode maintenance pour éviter des interruptions de service.

3. **Sauvegarde et restauration du cluster**
   - Sauvegardez régulièrement les configurations de votre cluster pour simplifier la récupération en cas de panne.

---

### 📚 Exercice Pratique

1. **Créer un cluster simple** entre deux serveurs Windows.
2. **Valider la configuration** et corriger les éventuels problèmes.
3. **Ajouter une application de test** (par exemple, un service IIS) pour observer le basculement automatique.

---

### 🧠 Conclusion

Un Cluster de Basculement est une solution puissante pour garantir la haute disponibilité et la résilience de vos applications critiques. Ce cours vous a montré comment le configurer, le surveiller, et le maintenir pour une performance optimale.
