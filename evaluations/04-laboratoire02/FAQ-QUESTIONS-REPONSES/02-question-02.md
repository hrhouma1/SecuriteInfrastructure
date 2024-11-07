**Question :**  
*Dans le cadre de la configuration d’un cluster NLB, comment savoir quel ordinateur prend le relais en cas de basculement, quelle adresse IP utiliser pour le DNS, et comment configurer les cartes réseau pour assurer une répartition de charge efficace ?*

---

**Réponse :**

Pour bien comprendre la configuration du cluster NLB et savoir quelle adresse utiliser pour le DNS, voici les détails :

1. **Adresse du cluster NLB (VIP)** :
   - Le cluster NLB utilise une **adresse IP virtuelle (VIP)** qui est partagée entre tous les nœuds (serveurs) du cluster. Dans ce laboratoire, cette VIP est **192.168.0.111**.
   - C'est l'adresse à laquelle les clients (utilisateurs ou applications) se connectent pour accéder au service. Une fois la connexion établie à cette adresse, le NLB redirige les requêtes vers l'un des serveurs disponibles en fonction de la charge.

2. **Configuration DNS** :
   - Pour que les clients puissent accéder au cluster via un nom de domaine, vous devez configurer le **DNS** en ajoutant un enregistrement **A** pour le cluster. 
   - Dans la configuration DNS, associez le nom de domaine du cluster (`ClusterWeb.test.local`) à l’adresse IP virtuelle du cluster, soit **192.168.0.111**. Cela signifie que lorsque des utilisateurs recherchent `ClusterWeb.test.local` ou `www.test.local`, ils seront redirigés vers la VIP du cluster. Le NLB gère ensuite la répartition de charge entre les serveurs disponibles.

3. **Configuration des deux cartes réseau par serveur** :
   - Chaque serveur participant au cluster doit avoir **deux cartes réseau** configurées pour des fonctions distinctes :
     - **Carte 1** : Utilisée pour la gestion du serveur, avec une adresse IP unique pour chaque serveur. Par exemple :
       - Serveur 1 : IP de gestion `192.168.0.1`
       - Serveur 2 : IP de gestion `192.168.0.2`
       - Serveur 3 : IP de gestion `192.168.0.3`
     - **Carte 2** : Utilisée pour le trafic NLB, avec une adresse IP dédiée pour chaque serveur dans le cluster. Par exemple :
       - Serveur 1 : IP NLB `192.168.0.11`
       - Serveur 2 : IP NLB `192.168.0.22`
       - Serveur 3 : IP NLB `192.168.0.33`

4. **Comment le basculement fonctionne-t-il avec le DNS et la VIP** :
   - Les clients et services extérieurs doivent utiliser l'adresse **192.168.0.111** (VIP) pour accéder au cluster.
   - Dans le DNS, vous ajoutez un enregistrement de type **A** pointant `ClusterWeb.test.local` vers **192.168.0.111**.
   - Si vous souhaitez utiliser un nom plus convivial comme `www.test.local`, vous pouvez ajouter un enregistrement **CNAME** pour `www.test.local` pointant vers `ClusterWeb.test.local`.

   En utilisant cette configuration, les clients se connectent à une adresse unique, **192.168.0.111** ou `ClusterWeb.test.local`, sans avoir besoin de connaître les adresses IP spécifiques des serveurs du cluster. Le NLB s’occupe de rediriger les demandes vers les serveurs actifs et disponibles, assurant ainsi une répartition de charge et un basculement automatique en cas de panne d’un des serveurs.

### Résumé de la configuration

- **Pour le DNS** : Ajoutez un enregistrement de type **A** dans le DNS pour `ClusterWeb.test.local`, pointant vers **192.168.0.111** (VIP du cluster).
- **Accès client** : Les clients utilisent l’adresse **192.168.0.111** ou `ClusterWeb.test.local` pour se connecter. Le NLB s’occupe de la répartition de charge entre les serveurs disponibles du cluster.
  
Cette configuration assure que les demandes sont réparties efficacement entre les serveurs et que le service reste disponible même si l'un des nœuds tombe en panne.
