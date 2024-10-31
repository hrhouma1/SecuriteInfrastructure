# Cluster de basculement Windows Server

## Table des matières
1. [Description générale](#description-générale)
2. [Objectifs et avantages du clustering de basculement](#objectifs-et-avantages-du-clustering-de-basculement)
3. [Concepts fondamentaux du clustering de basculement](#concepts-fondamentaux-du-clustering-de-basculement)
4. [Architecture d’un cluster de basculement](#architecture-dun-cluster-de-basculement)
5. [Fonctionnalité de volume partagé de cluster (CSV)](#fonctionnalité-de-volume-partagé-de-cluster-csv)
6. [Redondance et résilience dans les clusters](#redondance-et-résilience-dans-les-clusters)
7. [Sécurité et surveillance des clusters](#sécurité-et-surveillance-des-clusters)
8. [Bonnes pratiques théoriques pour les clusters de basculement](#bonnes-pratiques-théoriques-pour-les-clusters-de-basculement)

---

## 1. Description générale

Un **cluster de basculement** est une configuration informatique dans laquelle plusieurs serveurs (appelés **nœuds**) travaillent ensemble pour offrir une haute disponibilité des services. En cas de défaillance d’un ou plusieurs nœuds, les autres prennent automatiquement le relais, minimisant les interruptions de service. Ces clusters sont essentiels dans les environnements critiques où une continuité de service est requise.

Le clustering de basculement garantit que les applications et services, tels que les bases de données, les applications web ou le stockage de fichiers, restent disponibles même en cas de panne d’un composant matériel ou logiciel.

---

## 2. Objectifs et avantages du clustering de basculement

### Objectifs
- **Disponibilité accrue** : Réduire les interruptions en garantissant que les services critiques restent accessibles en basculant automatiquement les ressources d’un nœud défaillant à un autre.
- **Tolérance aux pannes** : Améliorer la résilience des applications contre les défaillances matérielles, logicielles et réseau.
- **Extensibilité** : Permettre l’ajout de nœuds au cluster pour augmenter la capacité ou la performance.

### Avantages
- **Sécurité opérationnelle** : En cas de panne, les utilisateurs ne subissent qu’une interruption minimale, car le service bascule automatiquement sur un autre nœud.
- **Gestion centralisée** : Un cluster de basculement permet une gestion unifiée de tous les nœuds.
- **Évolutivité** : Possibilité d’ajouter des ressources pour soutenir la croissance des applications.

---

## 3. Concepts fondamentaux du clustering de basculement

### Nœuds
Un **nœud** est un serveur au sein du cluster. Chaque nœud dispose de ressources matérielles et logicielles lui permettant de prendre en charge les services du cluster en cas de défaillance d’un autre nœud.

### Rôles en cluster
Les **rôles** (applications ou services) peuvent être configurés pour fonctionner sur le cluster. Chaque rôle est surveillé pour s'assurer de sa disponibilité. En cas de défaillance, le rôle est déplacé vers un autre nœud.

### Quorum
Le **quorum** est le mécanisme permettant de décider si le cluster peut fonctionner correctement. Le quorum peut être atteint via un modèle basé sur le nombre de nœuds, un témoin disque (stockage partagé) ou un témoin fichier (sous forme de fichier sur un stockage accessible par le réseau). Il garantit que le cluster a toujours la majorité des nœuds actifs pour éviter les divisions de réseau ou des situations de conflits.

### Basculement (Failover)
Le **basculement** est le processus qui consiste à transférer automatiquement un rôle du nœud défaillant vers un autre nœud du cluster pour maintenir la disponibilité.

### Clustering avec basculement
Le **clustering avec basculement** est la capacité du cluster à rediriger les services d’un nœud défaillant vers un nœud fonctionnel, réduisant ainsi les interruptions de service.

---

## 4. Architecture d’un cluster de basculement

### Configuration typique
Un cluster de basculement peut inclure plusieurs serveurs, appelés **nœuds**, reliés via un réseau haute disponibilité. En général, chaque nœud partage un stockage commun pour permettre aux rôles en cluster d’accéder aux mêmes données.

### Composants de base
- **Contrôleur de domaine** : Authentifie les nœuds et assure une gestion centralisée.
- **Serveurs en cluster** : Fonctionnent en tant que nœuds pour supporter les rôles.
- **Stockage partagé** : Disques accessibles par tous les nœuds pour héberger les données partagées.
  
### Types de stockage
Le stockage peut être connecté via iSCSI, Fibre Channel ou tout autre réseau de stockage partagé. Chaque nœud doit pouvoir accéder aux disques pour garantir un basculement sans interruption.

---

## 5. Fonctionnalité de volume partagé de cluster (CSV)

Les **Cluster Shared Volumes (CSV)** offrent un espace de noms distribué et permettent à tous les nœuds du cluster d’accéder simultanément aux mêmes volumes de stockage. Les CSV sont particulièrement utiles dans des environnements où des applications, comme des bases de données ou des machines virtuelles, nécessitent un accès partagé aux données pour un fonctionnement en haute disponibilité.

---

## 6. Redondance et résilience dans les clusters

### Redondance réseau
Pour assurer une disponibilité continue, un cluster de basculement nécessite des connexions réseau redondantes. En cas de panne d’une connexion, la redondance permet de maintenir les communications entre les nœuds.

### Redondance du stockage
Le stockage partagé utilisé dans un cluster doit également être redondant. Cela peut être accompli en utilisant un RAID (Redundant Array of Independent Disks) ou un système de stockage distribué. La redondance dans le stockage assure que les données restent accessibles même en cas de panne matérielle.

### Tolérance aux pannes
La tolérance aux pannes est la capacité du cluster à continuer de fonctionner en cas de défaillance d’un ou plusieurs composants. Elle est réalisée par la redondance dans les connexions réseau, les disques, et les serveurs.

---

## 7. Sécurité et surveillance des clusters

### Sécurité
La sécurité est essentielle dans un cluster de basculement, car plusieurs nœuds partagent les mêmes ressources. Les pratiques de sécurité comprennent :
- **Authentification et autorisation** : Utilisation de contrôles d’accès pour limiter l’accès au stockage et aux services du cluster.
- **Chiffrement** : Le chiffrement du stockage partagé pour protéger les données sensibles.
- **Mise à jour et gestion des correctifs** : Application de correctifs de sécurité réguliers pour protéger le cluster contre les vulnérabilités.

### Surveillance
Un cluster de basculement nécessite une surveillance continue pour garantir sa fiabilité :
- **Moniteur de disponibilité** : Vérifie si les nœuds et les rôles sont opérationnels.
- **Journaux d’événements** : Enregistre les incidents et les basculements pour faciliter le diagnostic.
- **Alertes et notifications** : Avertit les administrateurs en cas de défaillance ou de basculement d’un nœud.

---

## 8. Bonnes pratiques théoriques pour les clusters de basculement

### Validation du cluster
Effectuer une validation du cluster avant son utilisation en production permet de s’assurer que tous les composants fonctionnent correctement et sont prêts pour le basculement. Cela inclut la validation des connexions réseau, du stockage et de la configuration des nœuds.

### Documentation et plan de récupération
Documenter la configuration du cluster et établir un plan de récupération sont essentiels pour assurer la continuité de service en cas de problème. La documentation doit inclure les détails des configurations réseau, du stockage, et des paramètres des rôles.

### Planification de la maintenance
Planifier la maintenance des nœuds de manière à éviter des interruptions de service est crucial dans un cluster de basculement. Une maintenance planifiée doit inclure des mises à jour et des correctifs de sécurité appliqués individuellement à chaque nœud.

### Tests de basculement réguliers
Simuler régulièrement des basculements permet de tester la capacité du cluster à répondre aux défaillances et assure que le processus de basculement fonctionne comme attendu. Les tests peuvent inclure des basculements manuels et des simulations de pannes matérielles.

### Gestion du quorum et prévention de la partition réseau
Assurer une gestion adéquate du quorum pour éviter des situations de partition réseau (scénarios où les nœuds se retrouvent isolés) garantit la stabilité du cluster. Un quorum bien configuré aide à prévenir les conflits de ressources et les interruptions de service.



---------------------
## Annexe pratique:
---------------------

# Cluster de basculement sous Windows Server

## Table des matières
1. [Description de la fonctionnalité](#description-de-la-fonctionnalité)
2. [Architecture](#architecture)
3. [Configuration d’un espace de stockage redondant](#configuration-dun-espace-de-stockage-redondant)
   - [Création d’un pool de stockage](#création-dun-pool-de-stockage)
   - [Création d’un disque virtuel sur le pool](#création-dun-disque-virtuel-sur-le-pool)
   - [Création d’un volume sur un disque virtuel](#création-dun-volume-sur-un-disque-virtuel)
4. [La fonctionnalité d’un espace de stockage](#la-fonctionnalité-dun-espace-de-stockage)
   - [Installation des rôles et fonctionnalités nécessaires](#installation-des-rôles-et-fonctionnalités-nécessaires)
   - [Création des disques virtuels iSCSI et configuration de la cible](#création-des-disques-virtuels-iscsi-et-configuration-de-la-cible)
5. [Configuration de iSCSI sur les initiateurs (nœuds)](#configuration-de-iscsi-sur-les-initiateurs-nœuds)
6. [Installation et configuration du cluster de basculement](#installation-et-configuration-du-cluster-de-basculement)
   - [Installation](#installation)
   - [Configuration du rôle](#configuration-du-rôle)

---

## A. Description de la fonctionnalité

Un **cluster de basculement** est un ensemble de serveurs indépendants, appelés **nœuds**, travaillant ensemble pour garantir la disponibilité et la résilience des services hébergés. En cas de défaillance d’un nœud, les autres nœuds reprennent automatiquement ses charges de travail (processus de basculement). Cela permet une continuité de service avec des interruptions minimales.

Les clusters de basculement fonctionnent grâce à :
- Un système de surveillance proactive des rôles en cluster, redémarrant ou basculant les services en cas de défaillance.
- La fonctionnalité **Cluster Shared Volumes (CSV)**, offrant un espace de stockage partagé accessible par tous les nœuds pour les rôles en cluster.

La gestion s'effectue via l’outil **Gestionnaire de Cluster de basculement** ou les **cmdlets PowerShell**.

## B. Architecture

L’architecture pour mettre en place un cluster de basculement nécessite quatre machines :
- **Contrôleur de domaine** sous Windows Server (exemple : `domaine : test.local`).
- **Deux nœuds** sous Windows Server qui constituent le cluster de basculement.
- **Machine SAN iSCSI** contenant deux disques, configurés comme suit :
  - **Disque de Quorum** : environ 20 Go, vérifiant la disponibilité des nœuds.
  - **Disque de stockage** pour les données partagées entre les nœuds.

Les disques peuvent être connectés via iSCSI, SAS, SATA ou USB.

---

## C. Configuration d’un espace de stockage redondant

Le serveur de stockage doit disposer de plusieurs disques (exemple : trois disques de 30 Go). Voici les étapes de configuration :

### 1. Création d’un pool de stockage

1. Ouvrez le **Gestionnaire de serveur** > Tableau de bord.
2. Sélectionnez **Services de fichiers et de stockage**, puis cliquez sur **Nouveau Pool de stockage**.
3. Donnez un nom au Pool et sélectionnez les disques physiques souhaités (parité RAID5).
4. Cliquez sur **Créer** pour finaliser la création.

### 2. Création d’un disque virtuel sur le pool

1. Clic droit sur le pool de stockage créé > **Nouveau disque virtuel**.
2. Choisissez un nom et le type de stockage (RAID 0, RAID 1, ou RAID 5).
3. Définissez la taille du disque virtuel et finalisez en cliquant sur **Créer**.

### 3. Création d’un volume sur un disque virtuel

1. Lancez l’assistant de création de volume et suivez les étapes.
2. Assignez une lettre et un nom au volume, et choisissez le système de fichiers.
3. Cliquez sur **Créer** pour finaliser.

---

## D. La fonctionnalité d’un espace de stockage

Windows Server permet la création d’espaces de stockage redondants et extensibles avec des disques de tailles et types variés (SATA, SAS). La redondance peut être configurée selon les besoins en utilisant des pools de stockage avec des disques virtuels.

### 1. Installation des rôles et fonctionnalités nécessaires

1. Depuis le **Gestionnaire de serveur**, ajoutez le rôle **Serveur Cible iSCSI**.
2. Suivez l’assistant d’installation jusqu’à la fin.

### 2. Création des disques virtuels iSCSI et configuration de la cible

1. Allez dans **iSCSI** depuis le **Gestionnaire de serveur**.
2. Choisissez un emplacement, nommez le disque, et assignez-le à une cible iSCSI.
3. Ajoutez les initiateurs avec leurs adresses IP et confirmez.

---

## E. Configuration de iSCSI sur les initiateurs (nœuds)

1. Sur chaque nœud, ouvrez l’outil **Initiateur iSCSI**.
2. Entrez l’adresse IP ou le FQDN de la cible et connectez-vous.
3. Utilisez l’option **Configuration automatique** dans **Volumes et périphériques**.

---

## F. Installation et configuration du cluster de basculement

### 1. Installation

1. Sur chaque nœud, allez dans le **Gestionnaire de serveur** et choisissez **Installation basée sur un rôle ou une fonctionnalité**.
2. Sélectionnez **Clustering avec basculement** et terminez l’installation.

### 2. Configuration du rôle

1. Dans le **Gestionnaire de Cluster de basculement**, choisissez **Validez la configuration**.
2. Sélectionnez les nœuds et lancez les tests de validation.
3. Créez le cluster en entrant le nom et l’IP, et finalisez la configuration.


### G. Vérification et gestion du cluster de basculement

Une fois le cluster de basculement créé et configuré, il est essentiel de vérifier son bon fonctionnement et d’assurer sa maintenance. Les étapes suivantes vous permettront de gérer efficacement le cluster.

1. **Vérification de l’état du cluster** :
   - Dans le **Gestionnaire de Cluster de basculement**, vous pouvez consulter l’état de chaque nœud et de chaque rôle en cluster.
   - Assurez-vous que tous les nœuds sont en ligne et que les rôles assignés fonctionnent sans erreurs.
   
2. **Test de basculement manuel** :
   - Pour tester la résilience du cluster, vous pouvez réaliser un basculement manuel.
   - Dans le **Gestionnaire de Cluster de basculement**, faites un clic droit sur un rôle de cluster et sélectionnez **Déplacer** > **Sélectionner le nœud**. Choisissez un autre nœud pour vérifier si le service bascule correctement sans interruption.
   
3. **Surveillance continue** :
   - Utilisez l’option **Rapports** dans le Gestionnaire de Cluster pour obtenir des informations détaillées sur les événements de basculement, les alertes et les journaux d’événements.
   - Configurez des alertes pour être averti en cas de défaillance ou de basculement.

4. **Maintenance et mise à jour des nœuds** :
   - Lors de la maintenance ou de la mise à jour d’un nœud, effectuez un **basculement planifié** de ses services vers un autre nœud.
   - Dans le **Gestionnaire de Cluster**, utilisez la commande **Pause** sur le nœud pour le mettre en mode maintenance sans provoquer de défaillance de basculement.

### H. Gestion avancée des disques et du stockage

1. **Ajout de disques au pool de stockage** :
   - Si votre espace de stockage est proche de la saturation, vous pouvez ajouter des disques supplémentaires au pool de stockage.
   - Dans le **Gestionnaire de serveur** > **Services de fichiers et de stockage**, sélectionnez le pool de stockage, puis cliquez sur **Ajouter des disques** et suivez l’assistant.

2. **Gestion des volumes partagés en cluster (CSV)** :
   - La fonctionnalité **Cluster Shared Volumes (CSV)** permet à plusieurs nœuds d’accéder en simultané aux mêmes volumes de stockage, un atout pour les applications requérant un accès partagé (comme les bases de données).
   - Dans le **Gestionnaire de Cluster de basculement**, vous pouvez configurer les volumes CSV et spécifier les rôles qui y accèdent.

3. **Réparation de disques défectueux** :
   - En cas de défaillance d’un disque, vous pouvez retirer et remplacer le disque défectueux dans le pool de stockage.
   - Assurez-vous d’effectuer cette opération avec précaution pour ne pas compromettre l’intégrité des données. Vous pouvez également utiliser l’option **Réparer** dans le Gestionnaire de serveur pour reconfigurer le stockage après le remplacement du disque.

### I. Sécurité et redondance dans un cluster de basculement

1. **Redondance des connexions réseau** :
   - Dans une configuration de cluster, il est recommandé d’utiliser plusieurs connexions réseau pour chaque nœud (une pour les communications de gestion et une autre pour le stockage iSCSI, par exemple).
   - La redondance réseau garantit que la perte d’une connexion n’affecte pas la disponibilité du cluster.

2. **Sauvegardes régulières des configurations de cluster** :
   - Utilisez l’outil de sauvegarde intégré à Windows Server pour effectuer des sauvegardes régulières de la configuration du cluster.
   - En cas de défaillance, vous pouvez restaurer la configuration pour rétablir rapidement l’infrastructure.

3. **Sécurisation du stockage partagé** :
   - Configurez les disques partagés avec des autorisations restreintes pour limiter l’accès aux seuls nœuds du cluster.
   - Activez également le chiffrement BitLocker pour protéger les données sensibles sur les volumes de stockage partagés.

### J. Meilleures pratiques pour les clusters de basculement

1. **Validation régulière du cluster** :
   - Utilisez l’assistant de **Validation du cluster** pour effectuer des vérifications périodiques sur la configuration et la santé du cluster.
   - Cela permet de détecter des problèmes potentiels avant qu’ils n’affectent la production.

2. **Documentation et gestion des modifications** :
   - Tenez un registre des configurations et des modifications apportées au cluster pour faciliter le dépannage.
   - Chaque modification (ajout de nœud, modification de rôle, etc.) doit être documentée et vérifiée pour s’assurer qu’elle n’affecte pas les performances.

3. **Surveillance de la latence et de la charge réseau** :
   - Une latence élevée ou un trafic réseau congestionné peut affecter le fonctionnement du cluster.
   - Utilisez des outils de surveillance pour suivre la performance du réseau et ajuster les paramètres en conséquence.

### Conclusion

Un cluster de basculement sous Windows Server constitue une solution puissante pour garantir une haute disponibilité et une tolérance aux pannes. En suivant ces pratiques et en configurant les paramètres adéquats, vous pouvez déployer une infrastructure de basculement robuste et stable. Les étapes de gestion, de sécurité, et de maintenance permettent de maintenir un environnement performant, résilient et sécurisé, adapté aux besoins des entreprises modernes.
