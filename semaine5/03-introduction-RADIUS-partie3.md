# **Mise en place d’une infrastructure d'authentification centralisée**

# Objectif :  
L'objectif de cette semaine est d'apprendre à centraliser les connexions et les autorisations dans un réseau d'entreprise grâce à un serveur Active Directory Domain Services (AD DS) et d'autres outils. Ça veut dire que l'on veut gérer les connexions des utilisateurs d'un seul endroit central, plutôt que sur chaque ordinateur.

---------------
# **Étape 1 : Présentation ultra simplifiée d'un serveur NPS (Network Policy Server)**
---------------

# C'est quoi un serveur NPS ?
Imagine un serveur NPS comme le "portier" dans une grande entreprise. Ce portier est responsable de vérifier que toutes les personnes qui veulent entrer dans le bâtiment ont bien le droit d'y accéder. Le serveur NPS, c’est pareil, mais pour les connexions réseau. Il s’assure que seul le personnel autorisé peut accéder aux ressources (fichiers, imprimantes, Internet, etc.).

# Pourquoi utiliser un NPS ?
- **Centralisation :** Au lieu de vérifier l'identité des utilisateurs sur chaque ordinateur, le NPS s’en charge. Tout est géré d'un seul endroit.
- **Sécurité :** Le NPS peut aussi dire qui a le droit de faire quoi, par exemple, seulement certaines personnes peuvent accéder aux dossiers importants ou utiliser certains services.
- **Suivi :** Il enregistre qui s’est connecté, quand et d’où. Pratique pour garder une trace des activités.

# Comment ça marche ?
- Le NPS parle avec **Active Directory (AD)** pour vérifier l'identité des utilisateurs.
- Il gère aussi l'**autorisation**, c’est-à-dire qui peut faire quoi une fois connecté.

---

---------------
# **Étape 2 : Implémentation d'une solution Radius (Remote Authentication Dial-In User Service)**
---------------

# Qu'est-ce que Radius ?
Radius est comme le réseau de communication entre le portier (NPS) et tous les points d'entrée (comme les routeurs, points d'accès Wi-Fi, etc.). C'est lui qui fait passer les informations sur les identités des utilisateurs entre le NPS et les appareils.

# À quoi ça sert ?
Quand tu te connectes à un réseau Wi-Fi ou via un VPN (connexion sécurisée à distance), Radius s’assure que ton identité est vérifiée. Radius va demander au NPS : "Est-ce que cet utilisateur a le droit d'accéder au réseau ?"

# Comment mettre en place Radius ?
1. **Configurer le serveur Radius :**
   - On va installer un serveur NPS qui va aussi fonctionner comme un serveur Radius.
   - On configure les "clients Radius". Ce sont par exemple les points d'accès Wi-Fi ou les routeurs qui demandent l’autorisation d’accès pour les utilisateurs.
   
2. **Configurer l'authentification :**
   - On choisit la méthode d'authentification. Ici, on peut utiliser **EAP (Extensible Authentication Protocol)**, qui est un protocole sécurisé pour vérifier l'identité des utilisateurs (un peu comme un scan de badge mais pour les connexions).
   
3. **Connexion :**
   - Lorsqu'un utilisateur se connecte à un réseau, le routeur va envoyer une demande au serveur Radius, qui lui demandera à son tour au serveur NPS si cet utilisateur a bien les droits. Le NPS vérifie dans l'Active Directory, et si tout est bon, l'accès est autorisé.

---------------
# **Étape 3 : Test et journalisation (enregistrement des connexions)**
---------------

# Qu'est-ce que la journalisation ?
La journalisation, c'est comme un carnet où on note tout ce qui se passe. Ici, on va noter chaque tentative de connexion. Qui a essayé de se connecter ? À quelle heure ? Est-ce que ça a réussi ou non ?

# Pourquoi c'est important ?
Cela permet de :
- **Suivre les connexions** : savoir qui est connecté à quoi et quand.
- **Détecter des intrusions** : si quelqu’un essaie de se connecter de manière non autorisée, cela sera noté.
- **Faire du dépannage** : si un utilisateur a un problème pour se connecter, on peut consulter les journaux et comprendre pourquoi.

# Comment tester ?
1. **Configurer la journalisation sur NPS :**
   - On active l’enregistrement dans l’Event Viewer (visionneur d’événements) de Windows.
   - Chaque connexion ou tentative de connexion sera enregistrée.

2. **Faire un test de connexion :**
   - Utilisez un client (comme un PC ou un mobile) pour essayer de se connecter au réseau via un point d'accès Wi-Fi ou un VPN.
   - Allez voir dans les logs (journaux) du NPS pour vérifier si la connexion a bien été enregistrée.

---

# Exercice pratique (facile à suivre) :

1. **Installer et configurer NPS :**  
   - Installez un serveur NPS sur Windows Server.
   - Connectez-le à Active Directory pour qu’il puisse vérifier les identités.

2. **Configurer Radius :**  
   - Créez une règle dans NPS pour gérer les connexions via Wi-Fi ou VPN.
   - Configurez votre routeur ou point d’accès Wi-Fi pour envoyer les requêtes d’authentification au serveur NPS via Radius.

3. **Testez et vérifiez :**  
   - Connectez un utilisateur via Wi-Fi ou VPN.
   - Vérifiez dans les logs du NPS si la connexion a bien été enregistrée et si tout fonctionne correctement.

---

### Bonus : Questions simples pour mieux comprendre

- **Question 1 :** Pourquoi centraliser l'authentification ?  
  _Réponse :_ Cela permet de tout gérer à partir d'un seul endroit, ce qui est plus sécurisé et plus facile à maintenir.
  
- **Question 2 :** Qu'est-ce que Radius fait exactement ?  
  _Réponse :_ Radius envoie les informations de connexion d'un utilisateur au serveur NPS pour vérifier son identité et lui accorder l’accès ou non.

- **Question 3 :** Pourquoi la journalisation est-elle importante ?  
  _Réponse :_ Elle permet de savoir qui s’est connecté, quand, et de suivre les activités sur le réseau.

---

### Réflexions :
- **Imaginez que vous êtes l'administrateur réseau** : Comment vous assureriez-vous que seuls les bons utilisateurs puissent accéder au réseau ? Quels outils utiliseriez-vous pour surveiller les connexions et détecter des problèmes ?


