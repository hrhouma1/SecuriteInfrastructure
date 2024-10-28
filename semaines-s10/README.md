### **Performance et Haute Disponibilité des Infrastructures TI**

---

#### **Table des Matières**
1. [Introduction aux performances et à la haute disponibilité des TI](#intro)
2. [Les Fondamentaux de la Performance](#performance)
3. [Clusters de Serveurs Windows](#clusters-windows)
4. [Clusters de Serveurs Linux (LVS)](#clusters-linux)
5. [Haute Disponibilité avec Keepalived](#keepalived)
6. [TP 2 : Laboratoire sur la Haute Disponibilité et la Répartition de Charge](#tp2)
7. [Conclusion](#conclusion)

---

<a name="intro"></a>
### **1. Introduction aux performances et à la haute disponibilité des TI**

   - **Objectifs de la performance** : Assurer que les systèmes répondent de manière fiable et rapide aux requêtes des utilisateurs et supportent des charges variables.
   - **Problématiques courantes** :
     - Temps de réponse élevé
     - Charge réseau non optimisée
     - Risques de pannes

[Retour à la Table des Matières](#)

---

<a name="performance"></a>
### **2. Les Fondamentaux de la Performance**

   - **Comprendre la capacité et la charge** : Différence entre performance brute (capacité) et performance en charge (efficacité sous charge).
   - **Optimisation des ressources** : Utilisation optimale du CPU, de la RAM, et du réseau pour éviter la congestion.
   - **Tests de charge et de stress** : Techniques pour simuler des conditions extrêmes et évaluer la résilience des systèmes.

[Retour à la Table des Matières](#)

---

<a name="clusters-windows"></a>
### **3. Clusters de Serveurs Windows**

   - **Distribution Round-robin (RR)** :
     - Technique simple de répartition de charge où chaque requête est distribuée successivement aux serveurs disponibles.
     - Exemple pratique : Configurer RR sur un serveur DHCP pour voir comment les requêtes sont distribuées de manière circulaire.

   - **Réplication de charge réseau (NLB - Network Load Balancing)** :
     - Permet de distribuer les requêtes réseau sur plusieurs serveurs pour améliorer la performance.
     - Cas pratique : Configurer NLB pour gérer la répartition de charge entre plusieurs serveurs web sous Windows Server.

   - **Cluster de Basculement (Failover Cluster)** :
     - Sert de solution de haute disponibilité en permettant de basculer les services vers un serveur secondaire en cas de panne.
     - Exemple pratique : Créer un cluster de basculement sur un serveur Windows avec un serveur DHCP.

[Retour à la Table des Matières](#)

---

<a name="clusters-linux"></a>
### **4. Clusters de Serveurs Linux (Linux Virtual Server - LVS)**

   - **Équilibrage de charge avec LVS** :
     - **LVS-NAT (Network Address Translation)** : Redirige les requêtes vers des serveurs backend tout en masquant leur adresse IP réelle.
     - **LVS-DR (Direct Routing)** : Redirige les paquets vers des serveurs en gardant l'adresse IP d'origine, utile pour des configurations hautes performances.
     - **LVS-TUN (Tunneling)** : Utilisé pour envoyer le trafic vers des serveurs distants via des tunnels IP, permettant de gérer la répartition géographique des ressources.

   - **Cas pratique** : Configurer une solution LVS pour gérer la répartition de charge d’un serveur Web, en utilisant l’une des trois méthodes (NAT, DR, ou TUN).

[Retour à la Table des Matières](#)

---

<a name="keepalived"></a>
### **5. Haute Disponibilité avec Keepalived**

   - **Qu'est-ce que Keepalived ?**
     - Un outil de haute disponibilité pour Linux qui permet de surveiller et de basculer les services critiques entre plusieurs serveurs.
   - **Fonctionnement avec VRRP (Virtual Router Redundancy Protocol)** :
     - Utilisé pour éviter une interruption de service en basculant l'adresse IP virtuelle vers un autre serveur en cas de panne.
   - **Cas pratique** : Configurer Keepalived avec VRRP pour un serveur Web, pour observer le basculement automatique entre serveurs en cas de panne.

[Retour à la Table des Matières](#)

---

<a name="tp2"></a>
### **6. TP 2 : Laboratoire sur la Haute Disponibilité et la Répartition de Charge**

   - **Objectifs du TP** :
     - Configurer un serveur DHCP avec des solutions de haute disponibilité et de répartition de charge.
     - Utiliser RR, NLB, et Failover Cluster pour observer le fonctionnement et l’impact sur la performance.
     - Utiliser LVS avec différentes configurations (NAT, DR, TUN) et Keepalived pour un serveur Web.
     - Analyser les paquets VRRP capturés et observer les basculements automatiques grâce aux journaux.

   - **Étapes détaillées** :
     - **Étape 1** : Configurer un serveur DHCP avec RR et observer la distribution des requêtes.
     - **Étape 2** : Configurer un NLB pour répartir la charge sur plusieurs serveurs DHCP.
     - **Étape 3** : Mettre en place un Failover Cluster et simuler des pannes pour observer les basculements.
     - **Étape 4** : Configurer un serveur Web avec LVS (NAT, DR, et TUN) et observer l’équilibrage de charge.
     - **Étape 5** : Utiliser Keepalived pour tester la haute disponibilité avec VRRP.
     - **Étape 6** : Capturer et analyser les paquets VRRP, et lire les fichiers journaux pour valider le fonctionnement de la haute disponibilité.

[Retour à la Table des Matières](#)

---

<a name="conclusion"></a>
### **7. Conclusion**

Cette partie vous permettra de maîtriser les différentes techniques de répartition de charge et de haute disponibilité en utilisant des outils et protocoles avancés pour Windows et Linux. Les travaux pratiques renforcent l’apprentissage en permettant d’expérimenter directement ces concepts. 

[Retour à la Table des Matières](#)

---

